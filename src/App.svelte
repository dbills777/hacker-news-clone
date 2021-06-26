
	<script>
import moment from 'moment'

// use if URL fails--previously it failed
function shortenURL(link) {
    if (!link){
        return link = 'No Link to story'
    } else{
        return link.replace(/^(?:https?:\/\/)?(?:www\.)?/i, "").split('/')[0];
        }
    }
let allStories = []
async function getTopArticlesID(){
    const response = await fetch('https://hacker-news.firebaseio.com/v0/topstories.json')
    const data = await response.json()
    data.length = 30
    return data;
}
function getIndividualArticle(articleIDs){
    const mapItems = articleIDs.map(async article=>{
		const response = await fetch(`https://hacker-news.firebaseio.com/v0/item/${article}.json?print=pretty`)
		const data = await response.json()
		return data
	})
        return mapItems
}
getTopArticlesID()
.then(responses => getIndividualArticle(responses))
.then(finalResult => {
  Promise.all(finalResult).then(articles => {
			let result = articles.map(story =>({
				author: story.by,
				title: story.title,
				fullurl: story.url,
				url:  shortenURL(story.url),
				date: moment.unix(story.time),
				// urlParser: new URL(story.url),
				score: story.score,
				comments: story.descendants
			}))
			allStories = result
		})
})
</script>
<main>
	<header> <h3>Hacker News <span>   new | past | comments | ask | show | jobs | submit </span> </h3>  </header>
	{#each allStories as story, i}
		<div class="story">
			<p>{i + 1}. <span class="title"> <a class="title" href="{story.fullurl}">{story.title}</a></span>
			<span><a class ='url' href="{story.fullurl}" target="blank">({story.url})</a> </span>
			</p>
		<p class="nopadding url">  {story.score} points {moment(story.date).fromNow()} | hide | {story.comments} comments </p>
		</div>
	{/each}
</main>




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
	.title a{
		text-decoration: none;
		color: black;
	}
	.url{
		font-weight: 300;
		color: #828282;
		font-size: .75rem
	}
	.nopadding{
		padding: 0;
		margin: 0;
		margin-left: 16px;
	}
	.story{
		margin-left:5rem;
		margin-right: 5rem;
		margin-bottom: 1rem;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>