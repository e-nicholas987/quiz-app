<script>
  import Header from "./Header.svelte";
  import QuizContent from "./QuizContent.svelte";
  import Loading from "./Loading.svelte";

  async function getQuiz() {
    let response = await fetch(
      "https://opentdb.com/api.php?amount=10&category=18&type=multiple"
    );
	let quiz = await response.json();
	quiz = mapArr(quiz.results);
    // quiz = sortArr(quiz);
	;
	return quiz
  }

  function mapArr(arr) {
    return arr.map(item => {
      let question = item.question;
      let correct = item.correct_answer;
      let incorrect = item.incorrect_answers;
      let answers = [correct, ...incorrect];
      answers = sortArr(answers);
      return { question, answers, correct };
    });
  }

  function sortArr(arr) {
    return arr.sort((a, b) => 0.5 - Math.random());
  }
</script>

<Header />

{#await getQuiz()}
    <Loading/>
{:then quiz}
  <QuizContent {quiz}/>
  {:catch error}
  {error}Please refresh your broswer.
{/await}
