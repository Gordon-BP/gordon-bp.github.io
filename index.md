---
layout: default
---


# Recipe Bot Web Demo

This is a test page for hosting a Botpress Cloud bot in a Github Pages site.

## YouTube Playlist

You can follow along and build your own bot with the videos in [this playlist](https://www.youtube.com/playlist?list=PLlJHGGklthGnOzptoN_WV35UibNmZLMRE)


## Code Used

Calling the Spoonacular API [video 3](https://www.youtube.com/watch?v=cP9mWwC7rLE&list=PLlJHGGklthGnOzptoN_WV35UibNmZLMRE&index=3&t=6s&pp=gAQBiAQB)

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

Rendering the carousel [video 4](https://www.youtube.com/watch?v=qK0lm8ybWL8&list=PLlJHGGklthGnOzptoN_WV35UibNmZLMRE&index=4&pp=gAQBiAQB)

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

Preparing Chat History [video 5](https://www.youtube.com/watch?v=Enn6nGjmRzE&list=PLlJHGGklthGnOzptoN_WV35UibNmZLMRE&index=5&t=66s&pp=gAQBiAQB)

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
