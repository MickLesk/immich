<script lang="ts">
  import {
    notificationController,
    NotificationType,
  } from '$lib/components/shared-components/notification/notification';
  import { updateMyUser, type UserAdminResponseDto } from '@immich/sdk';
  import { fade } from 'svelte/transition';
  import { handleError } from '../../utils/handle-error';

  import SettingSwitch from '$lib/components/shared-components/settings/setting-switch.svelte';
  import Button from '../elements/buttons/button.svelte';
  import { t } from 'svelte-i18n';

  export let user: UserAdminResponseDto;

  const handleSave = async () => {
    try {
      const data = await updateMyUser({ userUpdateMeDto: { memoriesEnabled: user.memoriesEnabled } });

      Object.assign(user, data);

      notificationController.show({ message: $t('saved_settings'), type: NotificationType.Info });
    } catch (error) {
      handleError(error, $t('unable_to_update_settings'));
    }
  };
</script>

<section class="my-4">
  <div in:fade={{ duration: 500 }}>
    <form autocomplete="off" on:submit|preventDefault>
      <div class="ml-4 mt-4 flex flex-col gap-4">
        <div class="ml-4">
          <SettingSwitch
            id="time-based-memories"
            title={$t('time-based_memories')}
            subtitle={$t('photos_from_previous_years')}
            bind:checked={user.memoriesEnabled}
          />
        </div>
        <div class="flex justify-end">
          <Button type="submit" size="sm" on:click={() => handleSave()}>{$t('save')}</Button>
        </div>
      </div>
    </form>
  </div>
</section>
