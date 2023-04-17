---
layout: default
---


# Recipe Bot Web Demo

This is a webpage all about the Recipe Bot built using the new [Botpress Cloud Studio](https://botpress.com)

<iframe width="560" height="315" src="https://www.youtube.com/embed/yu1Sg_M_Ltc" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## YouTube Playlist

All of the project's videos are collected in [this playlist.](https://www.youtube.com/playlist?list=PLlJHGGklthGnOzptoN_WV35UibNmZLMRE)


## Code Used

Calling the Spoonacular API-

<iframe width="560" height="315" src="https://www.youtube.com/embed/cP9mWwC7rLE" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

```js
var options = {
  method: 'GET',
  url: 'https://api.spoonacular.com/recipes/complexSearch',
  params: {
    query: workflow.baseQuery,
    diet: encodeURIComponent(workflow.dietType),
    apiKey: env.apiKey,
    number: '3',
    ignorePantry: 'false',
    sort: 'popularity',
    sortDirection: 'asc',
    addRecipeInformation: 'true',
    addRecipeNutrition: 'false'
  }
}
const response = await axios.request(options)
if (response.status == 200) {
  workflow.recipes = response.data.results
} else {
  console.log(`API call failed with status code ${response.status}`)
}
```

Rendering the carousel-

<iframe width="560" height="315" src="https://www.youtube.com/embed/qK0lm8ybWL8" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

```js
workflow.recipeInfo = []
const myCards = workflow.recipes.map((recipe) => {
  workflow.recipeInfo.push({
    title: recipe.title,
    vegetarian: recipe.vegetarian,
    vegan: recipe.vegan,
    glutenFree: recipe.glutenFree,
    dairyFree: recipe.dairyFree,
    veryHealthy: recipe.veryHealthy,
    cheap: recipe.cheap,
    veryPopular: recipe.veryPopular,
    readyInMinutes: recipe.readyInMinutes,
    servings: recipe.servings,
    summary: recipe.summary
  })
  // create the card object
  return {
    type: 'card',
    title: {
      dynamicValue: `${recipe.title}`,
      valueType: 'dynamic'
    },
    subtitle: {
      dynamicValue: '',
      valueType: 'dynamic'
    },
    imageUrl: {
      dynamicValue: `${recipe.image}`,
      valueType: 'dynamic'
    },
    actions: [
      {
        action: 'url',
        label: 'View Recipe',
        value: `${recipe.sourceUrl}`
      }
    ]
  }
})
workflow.cards = []
for (var card of myCards) {
  workflow.cards.push({
    //in order to render a card, we only need these three fields
    title: card.title,
    imageUrl: card.imageUrl,
    actions: card.actions[0]
  })
}
```

Preparing Chat History-

<iframe width="560" height="315" src="https://www.youtube.com/embed/Enn6nGjmRzE" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>


```js
workflow.chatHistory = []
event.state.session.history.map((message) => {
  if (message.preview != 'What would you like to know about these recipes?') {
    workflow.chatHistory.push(`${message.sender}:${message.preview}`)
  }
})

const myStr = `Using the provided recipe information and chat history as context, try to answer the question as honestly as possible. If you don't know the answer, say "I don't know." 

Your answer should be friendly and complete, using the same words from the question as much as possible.
Recipe info: ${JSON.stringify(workflow.recipeInfo)}
Chat history: ${JSON.stringify(workflow.chatHistory)}
User question: ${event.preview}}`
console.log(`The prompt is ${myStr.length} characters long`)
if (myStr.length > 5000) {
  console.log('The prompt is too long! Omitting the chat history...')
  workflow.chatHistory = []
}
```
