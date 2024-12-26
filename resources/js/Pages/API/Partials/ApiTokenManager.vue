<script setup>
import { ref } from 'vue';
import { useForm } from '@inertiajs/vue3';
import ActionMessage from '@/Components/ActionMessage.vue';
import ActionSection from '@/Components/ActionSection.vue';
import Checkbox from '@/Components/Checkbox.vue';
import ConfirmationModal from '@/Components/ConfirmationModal.vue';
import DangerButton from '@/Components/DangerButton.vue';
import DialogModal from '@/Components/DialogModal.vue';
import FormSection from '@/Components/FormSection.vue';
import InputError from '@/Components/InputError.vue';
import InputLabel from '@/Components/InputLabel.vue';
import PrimaryButton from '@/Components/PrimaryButton.vue';
import SecondaryButton from '@/Components/SecondaryButton.vue';
import SectionBorder from '@/Components/SectionBorder.vue';
import TextInput from '@/Components/TextInput.vue';

const props = defineProps({
  tokens: Array,
  availablePermissions: Array,
  defaultPermissions: Array,
});

const createApiTokenForm = useForm({
  name: '',
  permissions: props.defaultPermissions,
});

const updateApiTokenForm = useForm({
  permissions: [],
});

const deleteApiTokenForm = useForm({});

const displayingToken = ref(false);
const managingPermissionsFor = ref(null);
const apiTokenBeingDeleted = ref(null);

const createApiToken = () => {
  createApiTokenForm.post(route('api-tokens.store'), {
    preserveScroll: true,
    onSuccess: () => {
      displayingToken.value = true;
      createApiTokenForm.reset();
    },
  });
};

const manageApiTokenPermissions = (token) => {
  updateApiTokenForm.permissions = token.abilities;
  managingPermissionsFor.value = token;
};

const updateApiToken = () => {
  updateApiTokenForm.put(route('api-tokens.update', managingPermissionsFor.value), {
    preserveScroll: true,
    preserveState: true,
    onSuccess: () => (managingPermissionsFor.value = null),
  });
};

const confirmApiTokenDeletion = (token) => {
  apiTokenBeingDeleted.value = token;
};

const deleteApiToken = () => {
  deleteApiTokenForm.delete(route('api-tokens.destroy', apiTokenBeingDeleted.value), {
    preserveScroll: true,
    preserveState: true,
    onSuccess: () => (apiTokenBeingDeleted.value = null),
  });
};
</script>

<template>
  <div>
    <!-- Generate API Token -->
    <FormSection @submitted="createApiToken">
      <template #title>
        Create API Token
      </template>

      <template #description>
        API tokens allow third-party services to authenticate with our application on your behalf.
      </template>

      <template #form>
        <!-- Token Name -->
        <div class="mb-3">
          <InputLabel for="name" value="Name"/>
          <TextInput
            id="name"
            v-model="createApiTokenForm.name"
            type="text"
            class="form-control"
            autofocus
          />
          <InputError :message="createApiTokenForm.errors.name" class="mt-2"/>
        </div>

        <!-- Token Permissions -->
        <div v-if="availablePermissions.length > 0" class="mb-3">
          <InputLabel for="permissions" value="Permissions"/>

          <div class="mt-2 row row-cols-1 row-cols-md-2">
            <div v-for="permission in availablePermissions" :key="permission" class="col">
              <label class="d-flex align-items-center">
                <Checkbox v-model:checked="createApiTokenForm.permissions" :value="permission"/>
                <span class="ms-2 text-sm text-muted">{{ permission }}</span>
              </label>
            </div>
          </div>
        </div>
      </template>

      <template #actions>
        <ActionMessage :on="createApiTokenForm.recentlySuccessful" class="me-3">
          Created.
        </ActionMessage>

        <PrimaryButton :class="{ 'opacity-25': createApiTokenForm.processing }" class="btn btn-primary"
                       :disabled="createApiTokenForm.processing">
          Create
        </PrimaryButton>
      </template>
    </FormSection>

    <div v-if="tokens.length > 0">
      <SectionBorder/>

      <!-- Manage API Tokens -->
      <div class="mt-4">
        <ActionSection>
          <template #title>
            Manage API Tokens
          </template>

          <template #description>
            You may delete any of your existing tokens if they are no longer needed.
          </template>

          <!-- API Token List -->
          <template #content>
            <div class="list-group">
              <div v-for="token in tokens" :key="token.id"
                   class="list-group-item d-flex justify-content-between align-items-center">
                <div class="text-truncate">
                  {{ token.name }}
                </div>

                <div class="d-flex">
                  <div v-if="token.last_used_ago" class="text-muted small me-2">
                    Last used {{ token.last_used_ago }}
                  </div>

                  <button
                    v-if="availablePermissions.length > 0"
                    class="btn btn-link btn-sm text-muted"
                    @click="manageApiTokenPermissions(token)"
                  >
                    Permissions
                  </button>

                  <button class="btn btn-link btn-sm text-danger ms-3" @click="confirmApiTokenDeletion(token)">
                    Delete
                  </button>
                </div>
              </div>
            </div>
          </template>
        </ActionSection>
      </div>
    </div>

    <!-- Token Value Modal -->
    <DialogModal v-show="displayingToken" @close="displayingToken = false">
      <template #title>
        API Token
      </template>

      <template #content>
        <div>
          Please copy your new API token. For your security, it won't be shown again.
        </div>
        {{ $page.props.jetstream.flash }}

        <div v-if="$page.props.jetstream.flash.token"
             class="mt-3 bg-light p-2 rounded font-monospace small text-muted break-all">
          {{ $page.props.jetstream.flash.token }}
        </div>
      </template>

      <template #footer>
        <SecondaryButton @click="displayingToken = false">
          Close
        </SecondaryButton>
      </template>
    </DialogModal>

    <!-- API Token Permissions Modal -->
    <DialogModal :show="managingPermissionsFor != null" @close="managingPermissionsFor = null">
      <template #title>
        API Token Permissions
      </template>

      <template #content>
        <div class="row row-cols-1 row-cols-md-2">
          <div v-for="permission in availablePermissions" :key="permission" class="col">
            <label class="d-flex align-items-center">
              <Checkbox v-model:checked="updateApiTokenForm.permissions" :value="permission"/>
              <span class="ms-2 text-sm text-muted">{{ permission }}</span>
            </label>
          </div>
        </div>
      </template>

      <template #footer>
        <SecondaryButton @click="managingPermissionsFor = null">
          Cancel
        </SecondaryButton>

        <PrimaryButton
          class="ms-3"
          :class="{ 'opacity-25': updateApiTokenForm.processing }"
          :disabled="updateApiTokenForm.processing"
          @click="updateApiToken"
        >
          Save
        </PrimaryButton>
      </template>
    </DialogModal>

    <!-- Delete Token Confirmation Modal -->
    <ConfirmationModal :show="apiTokenBeingDeleted != null" @close="apiTokenBeingDeleted = null">
      <template #title>
        Delete API Token
      </template>

      <template #content>
        Are you sure you want to delete this API token?
      </template>

      <template #footer>
        <SecondaryButton @click="apiTokenBeingDeleted = null">
          Cancel
        </SecondaryButton>

        <DangerButton
          class="ms-3"
          :class="{ 'opacity-25': deleteApiTokenForm.processing }"
          :disabled="deleteApiTokenForm.processing"
          @click="deleteApiToken">
          Delete
        </DangerButton>
      </template>
    </ConfirmationModal>
  </div>
</template>
