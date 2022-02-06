<script lang="ts">
  import { createEventDispatcher } from 'svelte';

  const dispatch = createEventDispatcher(),
    keyboard = [
      ['й', 'ц', 'у', 'к', 'е', 'н', 'г', 'ш', 'щ', 'з', 'х', 'ъ'],
      ['ф', 'ы', 'в', 'а', 'п', 'р', 'о', 'л', 'д', 'ж', 'э'],
      ['enter', 'я', 'ч', 'с', 'м', 'и', 'т', 'ь', 'б', 'ю', '<--']
    ];

  export let yellowKeys = [],
    greenKeys = [],
    blackKeys = [];
</script>

<section>
  {#each keyboard as row}
    <div class="row">
      {#each row as key}
        <button
          class={greenKeys.includes(key)
            ? 'green'
            : yellowKeys.includes(key)
            ? 'yellow'
            : blackKeys.includes(key)
            ? 'black'
            : ''}
          on:click={() => dispatch('click', key)}>{key}</button>
      {/each}
    </div>
  {/each}
</section>

<style>
  div {
    display: flex;
    justify-content: center;
    gap: 5px;
    margin-top: 5px;
  }
  button {
    font-size: 2.5rem;
    border: 1px solid white;
    border-radius: 10px;
    color: white;
    padding: 0.5rem;
    background: transparent;
    cursor: pointer;
    transition: background-color 1s ease;
  }
  .yellow {
    background-color: #947e00;
  }
  .green {
    background-color: #004100;
  }
  .black {
    background-color: #0f0f0f;
  }
</style>
