<script lang="ts">
  import { onMount } from "svelte";
  import {
    Button,
    Card,
    CardBody,
    CardFooter,
    CardHeader,
    CardSubtitle,
    CardText,
    CardTitle,
    Alert,
  } from "sveltestrap";

  import Answers from "./Answers.svelte";

  export let category = "0-Any Catagory";
  export let number = 10;

  console.log(category);

  let categoryName = category.split("-")[1];
  let categoryId = category.split("-")[0];
  let quizQuestions = [];
  let currentQuestion = 0;
  let quizQuestionLength = 0;
  let disabled = false;
  let score = 0;
  let correct = "unanswered";
  let nextText = "Next Question";
  let finish = false;

  const setDisabled = (val: boolean) => {
    disabled = val;
  };

  const setScore = (val: number) => {
    score = val;
  };

  const setCorrect = (val: string) => {
    correct = val;
  };

  const nextHandler = () => {
    if (currentQuestion < quizQuestions?.length - 1) currentQuestion += 1;
    else finish = true;

    if (currentQuestion == quizQuestions?.length - 1) nextText = "Finish Quiz";
    setDisabled(false);
    correct = "unanswered";
  };

  onMount(async () => {
    let url = "";
    if (categoryId == "0") url = `https://opentdb.com/api.php?amount=${number}`;
    else
      url = `https://opentdb.com/api.php?amount=${number}&category=${categoryId}`;
    let res = await fetch(url);
    let response = await res.json();
    quizQuestions = response.results;
    quizQuestionLength = response.results.length;
  });
</script>

{#if !finish}
  <Card class="mb-3">
    <CardHeader>
      Question #{currentQuestion + 1} of {quizQuestionLength}
      <br />
      Category: {categoryName}
    </CardHeader>
    {#if correct == "correct"}
      <Alert color="success">That answer is Correct!</Alert>
    {:else if correct == "incorrect"}
      <Alert color="danger">That answer is Incorrect!</Alert>
    {/if}
    <CardBody>
      <CardSubtitle>
        {@html quizQuestions[currentQuestion]?.question}
      </CardSubtitle>
      <br />
      <Answers
        incorrectAnswers={quizQuestions[currentQuestion]?.incorrect_answers}
        correctAnswer={quizQuestions[currentQuestion]?.correct_answer}
        {disabled}
        {score}
        {correct}
        {setDisabled}
        {setScore}
        {setCorrect}
      />
    </CardBody>
    {#if disabled}
      <CardFooter>
        <Button color="primary" on:click={nextHandler}>{nextText}</Button>
      </CardFooter>
    {/if}
  </Card>
{:else}
  <Card>
    <CardHeader>Quiz Complete!</CardHeader>
    <CardBody>
      <CardTitle>Score: {score} of {quizQuestions?.length}</CardTitle>
      <h1>{(score / quizQuestions?.length) * 100}%</h1>
    </CardBody>
  </Card>
{/if}
