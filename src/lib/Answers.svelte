<script lang="ts">
  import { Button } from "sveltestrap";

  export let correctAnswer = "";
  export let incorrectAnswers = [];
  export let disabled;
  export let score;
  export let correct;
  export let setDisabled;
  export let setScore;
  export let setCorrect;

  let selectedAnswer = "";

  let answerss;
  let shuffledAnswers;

  const handleClick = (e) => {
    selectedAnswer = e.target.value;
    setDisabled(true);
    if (e.target.value === correctAnswer) {
      setScore(score + 1);
      setCorrect("correct");
    } else {
      setCorrect("incorrect");
    }
  };

  $: setClass = (answer: string) => {
    if (correct == "incorrect" && answer == correctAnswer) {
      return "success";
    } else if (correct == "incorrect" && answer == selectedAnswer) {
      return "danger";
    } else if (correct == "correct" && answer == correctAnswer) {
      return "success";
    } else {
      return "primary";
    }
  };

  $: (() => {
    answerss = incorrectAnswers?.concat(correctAnswer);
    shuffledAnswers = answerss?.sort(() => Math.random() - 0.5);
    console.log("effect fired");
  })();
</script>

{#each shuffledAnswers as answer}
  <Button
    {disabled}
    color={setClass(answer)}
    style={"margin: 1px"}
    value={answer}
    on:click={handleClick}
    block
  >
    {@html answer}
  </Button>
{/each}
