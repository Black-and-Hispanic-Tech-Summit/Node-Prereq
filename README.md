# Introduction to Node

Node.js is a runtime environment that will run JavaScript code outside of a browser. It also comes with Node Package Manager or `npm`, a package manager that lets you install additional libraries.

Ensure that Node version >= 14.0.0 and npm version>= 5.6 are installed on your machine. You can install node [here](https://nodejs.org/en/).

You can check your Node version by running the following command in your terminal:

```
node -v
```

## A Node Application for Statistics

We'll be going through a small example project to get some practical, hands-on exposure to Node. Let's create an application that can provide some core statistics about some data.

We'll start in `src/statistics.js` by calculating the median of a list of numbers. The median is the value separating the higher half from the lower half of a dataset. It can informally be considered the middle of the dataset sequentially. Since it treats the dataset as a ordered sequence, we will first start by sorting our data in place using the `sort` list method:

```jsx
// src/statistics.js
// Returns the middle value of a list
function median(numbers) {
    numbers.sort((a, b) => a - b);
    
}
```

By default, `sort` will sort items alphabetically, even numbers. So we provide a small function that tells `sort` how to compare two numbers instead.
If `a - b` is negative then `sort` will know that `b` is greater and should appear later in the list than `a`, and if `a - b` is positive then it will know that `a` is greater and should appear later in the list than `b`.

Now we can add the logic for finding the median. If there is an odd number of items in the sorted dataset, then the median is simply the middle item. If there is an even number, then the median is considered the midpoint between the middle two numbers, or the average of the two:

```jsx
// src/statistics.js
// Returns the middle value of a list
function median(numbers) {
    numbers.sort((a, b) => a - b);
    let length = numbers.length;
    // Odd number of items, so median is the middle item
    if (length % 2 == 1) {
        return numbers[Math.floor(length / 2)];
    }
    // Even number of items, so median is the average of the middle two items
    else {
        let firstMiddle = numbers[(length / 2) - 1];
        let secondMiddle = numbers[length / 2];
        return (firstMiddle + secondMiddle) / 2;
    }
}
```

Now we need to export our function so that we can use it in `src/app.js`. We can do so by utilizing the `module.exports` variable at the bottom of the file to export our `median` function by name:

```jsx
// src/statistics.js
function median(numbers) {
    numbers.sort((a, b) => a - b);
    let length = numbers.length;
    // Odd number of items, so median is the middle item
    if (length % 2 == 1) {
        return numbers[Math.floor(length / 2)];
    }
    // Even number of items, so median is the average of the middle two items
    else {
        let firstMiddle = numbers[(length / 2) - 1];
        let secondMiddle = numbers[length / 2];
        return (firstMiddle + secondMiddle) / 2;
    }
}

module.exports = { median };
```

## Adding Another Statistic

Another immensely valuable statistic is the mean, or the average. This gives us a summary of our data by summing all the values and dividing it by the number of items in the dataset. Combining median and mean can give you a general overview of the data and how it's distributed by seeing how the middle partition of the data (median) relates to the average across the dataset (mean).

Let's add this statistic to our library. We'll start by creating an empty `mean` function in `src/statistics.js` below our `median` function and adding it to our exports:

```jsx
// src/statistics.js
// Returns the average of a list of numbers
function mean(numbers) {

}

module.exports = { median, mean };
```

Now we can add in the logic for calculating the mean. To do so, we'll need to add up all of our numbers and divide it by the length. To add up all the numbers, we'll utilize a special list method called `reduce` that accepts a function and an optional value as parameters:

```jsx
items.reduce(
    func,
    optionalValue
);
```

`reduce` will iterate over each item in the list and pass it into the provided function along with the return value of the function call on the previous item.

We can create an arrow function that accepts the previous return value of the function call alongside the current value being passed in. This allows our function to add the current value to the previous value that was returned in order to get the sum up to that point:

```jsx
(runningSum, currentValue) => runningSum + currentValue
```

We will also provide an initial value of `0` for the optional second parameter of `reduce` to use for `runningSum` in the first call to our arrow function since there is not yet a previous return value to use. Once `reduce` has iterated over all the items, it will return the accumulated item (in our case, a running sum) that was passed into each call. This will serve as the sum of all the item which we then divide by the number of items to get the mean:

```jsx
// src/statistics.js
// Returns the average of a list of numbers
function mean(numbers) {
    let initialValue = 0;
    let sum = numbers.reduce(
        (runningSum, currentValue) => runningSum + currentValue,
        initialValue
    );
    return sum / numbers.length;
}
```

## Utilizing our Statistics Library

Now we can utilize our statistics library in our `src/app.js` by importing it alongside the dataset we'll be using. We can do so by using the `require` function along with the relative paths of our `src/statistics.js` and `src/data.js` files:

```jsx
// src/app.js
const statistics = require('./statistics');
const { dataset } = require('./data');
```

Here we imported `statistics` which is an object containing our `median` and `mean` functions. They can be accessed with `statistics.median` and `statistics.mean`. We also imported our dataset by name which is denoted by the curly braces. This means instead of import our object as data and accessing it via `data.dataset`, we can directly import it by name and access it as its own variable.

Our dataset is a list of lists, which can be thought of as a table with rows of data. We can access each row by iterating through `dataset` by using the `forEach` iterative method. This method takes a function as a parameter to call on each item in the collection. That function itself will accept a variable as a parameter, and `forEach` will iterate over the collection and call the supplied function for each value in the collection. In this case, it iterates over the dataset and provides each data row as the value:

```jsx
// src/app.js
const statistics = require('./statistics');
const { dataset } = require('./data');

dataset.forEach(dataRow => {
    
});
```

Now we can provide some informative print statements via `console.log` and call our statistics methods on each row of data:

```jsx
// src/app.js
const statistics = require('./statistics');
const { dataset } = require('./data');

dataset.forEach(dataRow => {
    console.log("Data: " + dataRow)
    console.log("Median: " + statistics.median(dataRow));
    console.log("Mean: " + statistics.mean(dataRow));
});
```

## Running the Application

To run the application, simply run the following command in your terminal from within the `src` folder:

```
node app.js
```

The expected output for the provided data is:
```
Data: 1,4,8,5,10,6,5,2,5,10
Median: 5
Mean: 5.6
Data: 1,3,8,7,8,7,4,2,4,10
Median: 5.5
Mean: 5.4
Data: 1,1,2,2,5,6,6,8,8
Median: 5
Mean: 4.333333333333333
```

Congratulations on your first Node application!

<details>
<summary>Completed files</summary>
<br>

> ### statistics.js
> ```jsx
> // Returns the middle value of a list
> function median(numbers) {
>     numbers.sort((a, b) => a - b);
>     let length = numbers.length;
>     // Odd number of items, so median is the middle item
>     if (length % 2 == 1) {
>         return numbers[Math.floor(length / 2)];
>     }
>     // Even number of items, so median is the average of the middle two items
>     else {
>         let firstMiddle = numbers[(length / 2) - 1];
>         let secondMiddle = numbers[length / 2];
>         return (firstMiddle + secondMiddle) / 2;
>     }
> }
> 
> // Returns the average of a list of numbers
> function mean(numbers) {
>     let initialValue = 0;
>     let sum = numbers.reduce(
>         (runningSum, currentValue) => runningSum + currentValue,
>         initialValue
>     );
>     return sum / numbers.length;
> }
> 
> module.exports = { median, mean };
> ```
> 
> ### app.js
> ```jsx
> const statistics = require('./statistics');
> const { dataset } = require('./data');
> 
> dataset.forEach(dataRow => {
>     console.log("Data: " + dataRow)
>     console.log("Median: " + statistics.median(dataRow));
>     console.log("Mean: " + statistics.mean(dataRow));
> });
> ```
> 
> ### data.js
> ```jsx
> exports.dataset = [
>     [1, 4, 8, 5, 10, 6, 5, 2, 5, 10],
>     [1, 3, 8, 7, 8, 7, 4, 2, 4, 10],
>     [1, 1, 2, 2, 5, 6, 6, 8, 8]
> ];
> ```

</details>
<br>