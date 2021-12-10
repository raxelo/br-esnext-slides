---
theme: unicorn
class: text-center
highlighter: shiki
lineNumbers: false
info: |
  ## SS 2.1 & ESNext 
drawings:
  persist: false
title: SS 2.1 & ESNext slides
---

#### Why you should start using
# SS 2.1 & ESNext

<br>
<br>

A quick overview of the nicest features you can use to improve your code.

---

# Async / Await
<small>Improve your code's readability when working with promises.</small>

<div class="transform scale-75 origin-top">
  Generic Example

```js {all|4-5|7-8|11-12|15-19|all}
  async function getOnlineUsers() {
    // 👉 async functions always return a promise.

    // 📄 If we return a value, it will be passed as Promise.resolve(value).
    if (store.hideOnlineUsers()) return [];

    // ❌ If we throw an error, it will be passed as Promise.reject(error).
    if (store.isOffline()) throw new Error("❌ We're offline!");


    // We can use the 'await' keyword to wait for asynchronous code to run ⏱️.
    const storeId = await getStoreIdAsynchronously(); // without the 'await', storeId would be a promise.
    

    // We can return promises and they will be chained to this async function.
    return https.get.promise({
        url: 'https://www.testwebsite.com',
        headers: { storeId } // same as { storeId: storeId }
    })
  }

  // Print online users
  getOnlineUsers.then(console.log); // same as getOnlineUsers.then(result => console.log(result));
```

</div>


<br>


---

<div class="transform scale-75 origin-top -mt-4">
  NetSuite example

```js {all|5-6,24-30|8-12|14-21|all}
  // Given an array of user IDs, returns an array of their emails.
  // ⚠️ Runs a lookupField for every ID, asynchronously.
  async function getEmails(userIds) {
    
    // ⌛ This line will execute almost immediately, because the data fetching is asynchronous.
    const promises = userIds.map(id => createLookupFieldPromise(id))
    
    // There are many ways to return the results...

    // Promise.all: this will fail if ANY of the promises fail.
    const results = await Promise.all(promises)
    return results.map(result => result.email)

    // Promise.allSettled: this will return failed and successful promises in an array of results
    // results can have the following form: { status: 'fulfilled' | 'rejected', value?: any, reason?: string }
    const allResults = await Promise.allSettled(promises)

    // Return all emails from fulfilled promises.
    return allResult
      .filter(result => result.status === 'fulfilled')
      .map(result => result.value.email)
  }

  function createLookupFieldPromise(userId) {
    return NSearch.lookupFields.promise({
      type: NSearch.Type.CUSTOMER,
      id: userId,
      columns: 'email'
    })
  }

  // Print online users
  getEmails(['176', '175']).then(console.log); // same as getOnlineUsers.then(result => console.log(result));
  // prints ['lucas@brokenrubik.co', 'federico@brokenrubik.co']
  // the data is fetched in parallel.
```

</div>


<br>

---

## Oh, by the way: here are some nice features I couldn't add in the example

<div class="transform origin-top mt-4">
  Promise finally() was introduced in ES2018

  ```js
    // Print online users
    const promise = getEmails(['176', '175'])

    promise.then(console.log);
    promise.catch(console.error)
    promise.finally(() => console.log('Mi trabajo ha terminado.'))
  ```

</div>

<div class="transform origin-top mt-4">
  You can iterate through promises with a for loop

  ```js
    // gets an array of promises
    const promiseArray = getArrayOfPromises();

    // iterate throgh the array, waiting for each promise to resolve, one by one.
    for await (const result of promiseArray) {
      console.log(result) // prints the resolved value of the promise
    }
  ```

</div>


<br>

---

# Function parameters

<div class="transform scale-75 origin-top">

```js {all|4,21|all}
// Returns recent sales orders.
// By default applies a date filter to return sales orders in the last 7 days.
// A date can be specified to look for sales orders in a broader range.
function searchRecentSalesOrders(after = 'lastWeek') {
  return NSearch.create({
    type: NSearch.Type.SALES_ORDER,
    title: 'My Second SalesOrder Search',
    id: 'customsearch_my_second_so_search',
    columns: [{
        name: 'entity'
    }, {
        name: 'subsidiary'
    }, {
        name: 'name'
    }, {
        name: 'currency'
    }],
    filters: [{
        name: 'trandate',
        operator: 'after',
        values: [after] // by default, 'lastweek'
    }],
    settings: [{
        name: 'consolidationtype',
        value: 'AVERAGE'
    }]
  });
}

searchRecentSalesOrders() // returns sales orders after lastWeek
searchRecentSalesOrders('06/27/2021') // returns sales orders after '06/27/2021'

```

</div>
<br>

---

# Object destructuring & spread

<div class="transform scale-75 origin-top">

```js {all|1-14|all|15-16|18-20|all}
function fillDefaultValues(settings = {}) {
  const defaultValues = {
    maximumPlayers: 10,
    roomName: 'Rubik Battles 🟪',
    difficulty: 'easy'
  }

  // Spread operator
  return {
    ...defaultValues, // will spread the default values in this object
    ...settings // will replace default values if present
  }
}

fillDefaultValues({ roomName: 'My game room', maximumPlayers: 20 });
// {maximumPlayers: 20, roomName: 'My game room', difficulty: 'easy'}

// Destructuring
const { roomName } = fillDefaultValues();
// 'Rubik Battles 🟪'
```

</div>
<br>


---

# Array destructuring & spread

<div class="transform scale-75 origin-top">

```js {all|3-7|9-11|13-15|all}
// I couldn't come up with an interesting example 😔

const randomArray = [19, 'Lucas', 'Rocket League'];

// You can declare
let [age, name, favouriteGame] = randomArray;
console.log({ age, name, favouriteGame }); // { age: 19, name: 'Lucas', favouriteGame: 'Rocket League' }

// And re-declare (non-const variables)
[age, name, favouriteGame] = [20, 'Lucas', 'Minecraft'];
console.log({ age, name }); // { age: 20, name: 'Lucas', favouriteGame: 'Minecraft' }

// By the way, the spread operator is also available on arrays:
const arrayOfRandomThings = ['🍡', '🌀', '🐙', ...randomArray];
//  ['🍡', '🌀', '🐙', 19, 'Lucas']
```

</div>
<br>

---

# Optional chaining
And nullish coalescing (?? operator)

<div class="flex flex-col gap-4">

<div>

<div class="-mb-1">ES5</div>

```js
let blogThumbnailURL = '/default-thumbnail.webp';
if (blog.thumbnail && blog.thumbnail.url) {
  blogThumbnailURL = blog.thumbnail.url;
}
```

</div>

<div>

<div class="-mb-1">ESNext</div>

```js
let blogThumbnailURL = blog.thumbnail?.url ?? '/default-thumbnail.webp';
```

</div>

</div>
<br>

---

<div class="relative" style="margin-top: -2rem">

#### Array Methods
And why you should use them more often.

</div>

<div class="origin-top" style="transform: scale(0.65); margin-top: -1.25rem;">

```js {all|1-7|9-19|20-35|36-42|all}
const users = [
  { name: "Lucas", favoriteEmojis: ["🐙", "✨", "👀"], points: "50" },
  { name: "Ivan", favoriteEmojis: ["😆", "🚀", "❤️"], points: "50" },
  { name: "Fran", favoriteEmojis: ["🔥", "⚡", "🚀", "👀"], points: "15" },
  { name: "Nurio", favoriteEmojis: ["🍡"], points: "35" },
  { name: "Luis", favoriteEmojis: ["😎", "😳", "😔"], points: "0" },
];

function filterUniqueStrings(string, index, array) {
  return array.indexOf(string) === index;
}

function getEmojisOfUsersWithPointThreshold(points) {
  return users
    .filter(user => user.points >= points)
    .flatMap(user => user.favoriteEmojis)
    .filter(filterUniqueStrings) // avoid duplicate emojis
}

// Same function as above, with for's and if's.
function letsTryTheSameWithForLoops(points) {
  const emojis = [];
  for (const user of users) {
    if (user.points >= points) {
      for (const emoji of user.favoriteEmojis) {
        if (emojis.indexOf(emoji) === -1) {
          emojis.push(emoji)
        }
      }
    }
  }

  return emojis;
}

getEmojisOfUsersWithPointThreshold(0)
// (12) ['🐙', '✨', '👀', '😆', '🚀', '❤️', '🔥', '⚡', '🍡', '😎', '😳', '😔']
getEmojisOfUsersWithPointThreshold(30)
// (7) ['🐙', '✨', '👀', '😆', '🚀', '❤️', '🍡']
getEmojisOfUsersWithPointThreshold(50)
// (4) ['🐙', '✨', '👀', '😆', '🚀', '❤️']

```

</div>


---

<div class="transform scale-75 origin-top -mt-3">

```js 
const arrayOfArraysOfEmojis = [['❤️', '✨', '😆'], ['👀'], [], ['🟪', '🚀']];

const emojis = arrayOfArraysOfEmojis.flat(); // same as arrayOfArraysOfEmojis.flatMap(item => item) 
// (6) ['❤️', '✨', '😆', '👀', '🟪', '🚀']
```

</div>

---

# Finally
Template strings and replaceAll

<div>

```js 
// Since ES6 we have template strings, allowing us to build complex strings more easily, 
// allowing us to break lines and have special characters. 
function generateMailBody(user) {
  return `Hi ${user.name},
We hope you're having a fantastic day.

That's all, thank you for reading, ${user.name} 😊.
`;
}


// Since ES2021 we have replaceAll
const body = generateMailBody({ name: 'raxel' }).replaceAll('raxel', 'Lucas');
// before, we could use regex to achieve the same: string.replace(/raxel/g, 'Lucas');

alert(body);
```

</div>

<Alert />