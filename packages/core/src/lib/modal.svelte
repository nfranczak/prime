<!--
@component

Creates a modal overlay.

```svelte
    <div>
      <Button on:click={handleOpenModal}>Open Modal</Button>
      <Modal open={modalOpen} on:close={handleCloseModal}>
        <span slot="title">This is the modal demo</span>
        <span slot="message">Are you sure you want to print a statement to the console?</span>
        <Button
          slot="primary"
          on:click={() => console.log('statement')}
        >
          Print
        </Button>
        <Button slot="secondary" variant="dark" on:click={handleCloseModal}>
          Cancel
        </Button>
      </Modal>
    </div>
```
-->
<svelte:options immutable />

<script lang="ts">
import cx from 'classnames';
import { createEventDispatcher, onMount } from 'svelte';
import IconButton from './button/icon-button.svelte';

/** Whether the modal is open. */
export let open = false;
/** The variant of the modal. */
export let variant: 'small' | '' = '';

const dispatch = createEventDispatcher<{
  /** Fires when the modal is requested to close. */
  close: boolean;
}>();

const handleCloseModal = () => {
  dispatch('close', true);
};

const handleBackgroundClick = (event: MouseEvent) => {
  if (event.target === event.currentTarget) {
    handleCloseModal();
  }
};

const handleBackgroundKey = (event: KeyboardEvent) => {
  if (event.key === 'Enter' || event.key === 'Space') {
    handleCloseModal();
  }
};

onMount(() => {
  document.body.classList.toggle('overflow-hidden', open);
});
</script>

<div
  class={cx(
    'fixed left-0 top-0 z-50 flex h-full w-full items-center justify-center bg-black bg-opacity-40',
    {
      invisible: !open,
    }
  )}
  role="button"
  tabindex="0"
  on:click={handleBackgroundClick}
  on:keydown={handleBackgroundKey}
>
  <div
    class={cx('relative max-w-lg border border-medium bg-white p-6 shadow-sm', {
      'max-h-[600px] min-h-[400px] w-[480px]': variant === '',
      'max-h-[320px] w-[400px]': variant === 'small',
    })}
  >
    <IconButton
      class="absolute right-4 top-4 text-gray-6"
      icon="close"
      label="Close modal"
      on:click={handleCloseModal}
    />
    <figure
      class={cx('flex flex-col gap-2', { 'min-h-[400px]': variant === '' })}
    >
      <figcaption class="pr-12 text-lg font-semibold">
        <slot name="title" />
      </figcaption>

      <div class="text-sm text-subtle-1">
        <slot name="message" />
      </div>

      <div class="flex flex-grow"><slot /></div>

      <div class="mt-6 flex justify-end gap-2">
        <slot name="secondary" />
        <slot name="primary" />
      </div>
    </figure>
  </div>
</div>
