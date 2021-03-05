<style>
	main {
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
		background-color: #F6F6EF;
		font-size: 13.33px;
	}

	p {
		color: #000000;
		font-size:15px;
		font-weight: 100;
	}
	.title {
		font-weight: 400;
	}
	.url{
		font-weight: 100;
		color: #828282;
		font-size: .75rem
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
				url:  new URL(`${story.url}`),
				date: moment.unix(story.time)
			}))
			console.log(result)
			allStories = result
		})
	})

</script>

<main>

	{#each allStories as story, i}
		<p>{i + 1}. <span class="title"> {story.author} </span>
			<span><a class = 'url'href="{story.url}">{story.url.host}</a> </span>
			</p>
			<br>
			{moment(story.date).fromNow()}

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


