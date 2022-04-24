<script lang="ts">
  import { getRandomWord, doesWordExists } from './words';
  import Keyboard from './Keyboard.svelte';

  let word,
    attempts: { value: string; class: '' | 'green' | 'yellow' }[][],
    activeAttempt,
    yellowKeys,
    greenKeys,
    blackKeys;

  const reloadGame = () => {
      word = getRandomWord();
      attempts = new Array(6)
        .fill('')
        .map(() => new Array(5).fill('').map(() => ({ value: ' ', class: '' })));
      activeAttempt = 0;
      yellowKeys = [];
      greenKeys = [];
      blackKeys = [];
    },
    onKeyPress = e => {
      const key = e.key.toLowerCase();
      if (
        ![...Array.from('йцукенгшщзхъфывапролджэячсмитьбю'), 'backspace', 'enter', '<--'].includes(
          key
        )
      )
        return;
      switch (key) {
        case '<--':
        case 'backspace':
          if (attempts[activeAttempt][0].value !== ' ')
            attempts[activeAttempt][
              attempts[activeAttempt].findIndex(d => d.value === ' ') === -1
                ? 4
                : attempts[activeAttempt].findIndex(d => d.value === ' ') - 1
            ].value = ' ';
          break;
        case 'enter':
          if (attempts[activeAttempt].reduce((a, b) => a + b.value, '') === word) {
            alert('Вы выиграли');
            reloadGame();
            break;
          }
          if (attempts[activeAttempt].find(d => d.value === ' ')) break;
          if (!doesWordExists(attempts[activeAttempt].reduce((a, b) => a + b.value, ''))) {
            alert('Этого слова нет в нашем словаре');
            break;
          }
          let answer = Array.from(word);
          for (const i in attempts[activeAttempt])
            if (attempts[activeAttempt][i].value === answer[i]) {
              attempts[activeAttempt][i].class = 'green';
              answer[i] = ' ';
              greenKeys = [...greenKeys, attempts[activeAttempt][i].value];
            }
          for (const i in attempts[activeAttempt])
            if (answer.includes(attempts[activeAttempt][i].value)) {
              attempts[activeAttempt][i].class = 'yellow';
              answer[answer.indexOf(attempts[activeAttempt][i].value)] = ' ';
              yellowKeys = [...yellowKeys, attempts[activeAttempt][i].value];
            } else blackKeys = [...blackKeys, attempts[activeAttempt][i].value];

          activeAttempt++;
          if (activeAttempt > 5) {
            alert(`Вы проиграли. Слово было ${word}`);
            reloadGame();
            break;
          }
          break;
        default:
          if (attempts[activeAttempt][4].value === ' ') {
            attempts[activeAttempt][attempts[activeAttempt].findIndex(d => d.value === ' ')].value =
              key;
          }
      }
    };

  reloadGame();
</script>

<svelte:window on:keydown={onKeyPress} />

<header>
  <h1>ВОРДЛ</h1>
</header>
<main>
  <section>
    {#each attempts as attempt, i}
      <p class:active={activeAttempt === i}>
        {#each attempt as letter}
          <span class={letter.class}>
            {letter.value.toUpperCase()}
          </span>
        {/each}
      </p>
    {/each}
  </section>
  <Keyboard
    on:click={({ detail }) => onKeyPress({ key: detail })}
    {blackKeys}
    {yellowKeys}
    {greenKeys} />
</main>
<footer>
  <p>Сайт сделал Матвей Рябчиков</p>
  <a href="https://github.com/ronanru/wordle-ru" target="_blank" rel="noopener noreferrer">
    Посмотреть код сайта на GitHub
  </a>
</footer>

<style>
  h1 {
    margin: 1rem 0;
  }
  section {
    width: min-content;
    margin: 0 auto;
  }
  section p {
    display: grid;
    gap: 1rem;
    grid-template-columns: repeat(5, 1fr);
    margin: 2.5rem 0;
  }
  section p.active span {
    background: transparent;
  }
  span {
    border: 1px solid white;
    width: 5rem;
    height: 5rem;
    box-sizing: border-box;
    padding: 1rem;
    border-radius: 5px;
    display: inline-block;
    display: grid;
    place-items: center;
    background: #0f0f0f;
  }
  .yellow {
    background-color: #947e00;
  }
  .green {
    background-color: #004100;
  }
  footer {
    margin-top: 5rem;
    font-size: 0.5em;
    text-align: center;
  }
  a {
    color: white;
  }
</style>
