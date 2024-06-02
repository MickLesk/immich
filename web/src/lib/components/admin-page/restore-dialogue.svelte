<script lang="ts">
  import ConfirmDialogue from '$lib/components/shared-components/confirm-dialogue.svelte';
  import { handleError } from '$lib/utils/handle-error';
  import { restoreUserAdmin, type UserResponseDto } from '@immich/sdk';
  import { createEventDispatcher } from 'svelte';
  import { t } from 'svelte-i18n';

  export let user: UserResponseDto;

  const dispatch = createEventDispatcher<{
    success: void;
    fail: void;
    cancel: void;
  }>();

  const handleRestoreUser = async () => {
    try {
      const { deletedAt } = await restoreUserAdmin({ id: user.id });
      if (deletedAt == undefined) {
        dispatch('success');
      } else {
        dispatch('fail');
      }
    } catch (error) {
      handleError(error, $t('unable_to_restore_user'));
      dispatch('fail');
    }
  };
</script>

<ConfirmDialogue
  id="restore-user-modal"
  title={$t('restore_user')}
  confirmText={$t('continue')}
  confirmColor="green"
  onConfirm={handleRestoreUser}
  onClose={() => dispatch('cancel')}
>
  <svelte:fragment slot="prompt">
    <p><b>{user.name}</b>'s account will be restored.</p>
  </svelte:fragment>
</ConfirmDialogue>
