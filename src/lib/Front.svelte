<script lang="ts">
  import { onMount } from "svelte";
  import { Form, FormGroup, Input, Label, Button } from "sveltestrap";
  import Quizbox from "./Quizbox.svelte";
  let noOfQuestions = 10;
  let categories = [];
  let start = false;
  let categorySelected;

  const startQuiz = (e) => {
    e.preventDefault();
    start = true;
  };

  onMount(async () => {
    const res = await fetch("https://opentdb.com/api_category.php");
    let response = await res.json();
    categories = response.trivia_categories;
  });
</script>

{#if start}
  <Quizbox category={categorySelected} number={noOfQuestions} />
{:else}
  <Form on:submit={startQuiz} method="post">
    <FormGroup>
      <Label for="noOfQuestions">No. of Questions</Label>
      <Input
        type="number"
        name="number"
        id="noOfQuestions"
        bind:value={noOfQuestions}
        min="5"
        max="30"
      />
    </FormGroup>
    <FormGroup>
      <Label for="exampleSelect">Select</Label>
      <Input
        type="select"
        name="select"
        id="exampleSelect"
        bind:value={categorySelected}
      >
        <option>Any Category</option>
        {#each categories as category}
          <option value={category.id + "-" + category.name}
            >{category.name}</option
          >
        {/each}
      </Input>
    </FormGroup>
    <Button>Start Quiz</Button>
  </Form>
{/if}
