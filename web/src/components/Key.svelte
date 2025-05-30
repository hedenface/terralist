<script lang="ts">
  import TransparentButton from './TransparentButton.svelte';
  import Icon from './Icon.svelte';
  import Modal from './Modal.svelte';
  import ConfirmationModal from './ConfirmationModal.svelte';

  import type { Key } from '@/api/authorities';

  import { useFlag } from '@/lib/hooks';

  export let authorityKey: Key;
  export let authorityName: string;
  export let isAlone: boolean = false;
  export let onDelete: (id: string) => void = () => {};

  const [asciiArmorModalEnabled, showAsciiArmorModal, hideAsciiArmorModal] =
    useFlag(false);
  const [
    trustSignatureModalEnabled,
    showTrustSignatureModal,
    hideTrustSignatureModal
  ] = useFlag(false);
  const [deleteModalEnabled, showDeleteModal, hideDeleteModal] = useFlag(false);

  const remove = () => {
    onDelete(authorityKey.id);
  };
</script>

<div class="mt-2 mx-4">
  <div
    class="w-full rounded-lg p-2 px-6 bg-teal-400 dark:bg-teal-700 grid grid-cols-4 lg:grid-cols-8 place-items-start">
    <span class="break-all lg:col-span-5">
      {authorityKey.keyId}
    </span>
    <span>
      <TransparentButton onClick={showAsciiArmorModal}>
        <Icon name="eye" />
      </TransparentButton>
    </span>
    <span>
      <TransparentButton onClick={showTrustSignatureModal}>
        <Icon name="eye" />
      </TransparentButton>
    </span>
    <span class="place-self-end">
      <TransparentButton onClick={showDeleteModal}>
        <Icon name="trash" />
      </TransparentButton>
    </span>
  </div>
</div>

<Modal
  title={`ASCII Armor of Key ID ${authorityKey.keyId}`}
  enabled={$asciiArmorModalEnabled}
  onClose={hideAsciiArmorModal}>
  <span slot="body">
    <pre class="text-xs">{authorityKey.asciiArmor || '<empty>'}</pre>
  </span>
</Modal>

<Modal
  title={`Trust Signature of Key ID ${authorityKey.keyId}`}
  enabled={$trustSignatureModalEnabled}
  onClose={hideTrustSignatureModal}>
  <span slot="body">
    <pre class="text-xs">{authorityKey.trustSignature || '<empty>'}</pre>
  </span>
</Modal>

<ConfirmationModal
  title={`Remove Key ID ${authorityKey.keyId} of ${authorityName}`}
  enabled={$deleteModalEnabled}
  onClose={hideDeleteModal}
  onSubmit={remove}>
  {#if isAlone}
    This is the last key of <b>{authorityName}</b> authority. Removing it will
    also remove the authority and all artifacts uploaded to the
    <b>{authorityName}</b>
    namespace.
    <br /><br />
  {/if}
  Are you sure?
</ConfirmationModal>
