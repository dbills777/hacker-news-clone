<style>
	main {
	
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
		background-color: #F6F6EF;
		font-size: 13.33px;
	}
	header{
		background-color: #F66604;
	}
	header h3{
		padding-top: 1rem;
		padding-bottom: 1rem;
		margin-left:5rem;
		margin-right: 5rem;
	}
	header h3 span {
		padding-top: 1rem;
		padding-bottom: 1rem;
		margin-right: 5rem;
		font-weight: 200;
	}

	p {
		color: #000000;
		font-size:15px;
		font-weight: 100;
		padding: 0;
		margin: 0;
	}
	.title {
		font-weight: 400;
	}
	.url{
		font-weight: 100;
		color: #828282;
		font-size: .75rem
	}
	.nopadding{
		padding: 0;
		margin: 0;
	}
	.story{
		margin-left:5rem;
		margin-right: 5rem;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
	<script>
		import moment from 'moment'

		let getThirtyArticles = []
	async function getListOfStoryID() {
		const res = await fetch(`https://hacker-news.firebaseio.com/v0/topstories.json?print=pretty`);
		const text = await res.json();
		if (res.ok) {
			getThirtyArticles = text
			getThirtyArticles.length = 30
			console.log(getThirtyArticles)
			return getThirtyArticles;
		} else {
			throw new Error(text);
		}
	}
	let arraOfThirty = getListOfStoryID();

		let StoryArray = []
	async function getSingleStory(storyID) {
		const res = await fetch(`https://hacker-news.firebaseio.com/v0/item/${storyID}.json?print=pretty`);
		const text = await res.json();
		StoryArray.push(text)

		if (res.ok) {
			StoryArray = text
			StoryArray.length = 30
			return StoryArray;
		} else {
			throw new Error(text);
		}
	}
	let fetchlist = getListOfStoryID()
	let SingleStory = getSingleStory('26343577');



	let allStories= []
	let getMoreStories = fetchlist.then(item=>{
			const mapItems = item.map(async item=>{
				// console.log(item)
				const response = await fetch(`https://hacker-news.firebaseio.com/v0/item/${item}.json?print=pretty`)
				const data = await response.json()
				// console.log(data)
				return data
			})
			console.log(mapItems)

			return mapItems
		})

	getMoreStories.then(story =>{
		Promise.all(story).then(story => {
			const testChange = [...story]
			let result = testChange.map(story =>({
				author: story.by,
				title: story.title,
				url:  story.url,
				// newurl: new URL(`${story.url}`),
				date: moment.unix(story.time),
				score: story.score,
				comments: story.descendants
			}))
			console.log(result)
			allStories = result
		})
	})

</script>

<main>
	<header> <h3>Hacker News <span> new | past | comments | ask | show | jobs | submit </span> </h3>  </header>
	{#each allStories as story, i}
		<div class="story">
			<p>{i + 1}. <span class="title"> {story.title} </span>
			<span><a class ='url' href="{story.url}">({story.url})</a> </span>
			</p>
		<p class="nopadding url">  {story.score} points {moment(story.date).fromNow()} | hide | {story.comments} comments </p>
		</div>

			<br>


	{/each}


<!--
	{#await SingleStory}
		<p>...waiting</p>
	{:then SingleStory}
		<p> 1. {SingleStory.title}, {SingleStory.url}</p>
		<p>{SingleStory.score} points by {SingleStory.by} time as number{SingleStory.time} | {SingleStory.descendants} comments </p>
	{:catch error}
		<p style="color: red">{error.message}</p>
	{/await}
-->





</main>


