# Website Performance Optimization portfolio project

http://aviaryan.github.io/ud-website-optimization/

Udacity FEND project

The aim of this project is to optimize the critical rendering path and make the index page render as quickly as possible by applying the techniques picked up in the [Critical Rendering Path course](https://www.udacity.com/course/ud884).
Also we had to optimize FPS in pizza.html.


## Part 1: Optimize PageSpeed Insights score for index.html

To first start this project, we had to setup the project.

1. Check out the repository
1. To inspect the site on your phone, you can run a local server

  ```bash
  $> cd /path/to/your-project-folder
  $> python -m SimpleHTTPServer 8080
  ```

1. Open a browser and visit localhost:8080
1. Download and install [ngrok](https://ngrok.com/) to the top-level of your project directory to make your local server accessible remotely.

  ``` bash
  $> cd /path/to/your-project-folder
  $> ./ngrok http 8080
  ```

1. Copy the public URL ngrok gives you and try running it through PageSpeed Insights! Optional: [More on integrating ngrok, Grunt and PageSpeed.](http://www.jamescryer.com/2014/06/12/grunt-pagespeed-and-ngrok-locally-testing/)

### Optimizations

The following optimizations were done.

1. Inline `style.css` in the index.html file.
2. Use a `media=print` query on print.css so that it is only rendered when printing.
3. Move google analytics to the end of the `body` tag and made its script load async.
4. Use [WebFont](https://github.com/typekit/webfontloader) to load fonts so that it doesn't block rendering.
5. Compress `profilepic.jpg` to `profilepic_compressed.jpg` without sacrificing quality using ImageOptim.
6. Reduce dimensions of `pizerria.jpg` to `pizzeria_small.jpg` because width of that image on page is only 100px.


## Part 2: Optimize Frames per Second in pizza.html

In this, we had to modify `views/js/main.js`. FPS Counter/HUD Display useful in Chrome developer tools has been described here: [Chrome Dev Tools tips-and-tricks](https://developer.chrome.com/devtools/docs/tips-and-tricks).

To first test this, start `views/pizza.html` in the browser and open Chrome Dev Tools. Have a look at the Performance or Timeline tab.

### Optimizations

* `updatePositions` - Only 5 phases were needed so pre-calculated those and then used them for the scroll event. Also moved a static property fetch outside the loop.

```js
// .. code
  var phases = []; // store phases first and then set
  var scrollTop = document.body.scrollTop / 1250; // get this only once, var reuse

  var items = document.querySelectorAll('.mover');
  for (var i = 0; i < 5; i++) {
    // var phase = Math.sin((document.body.scrollTop / 1250) + (i % 5));
    // items[i].style.left = items[i].basicLeft + 100 * phase + 'px';
    phases.push(100 * Math.sin(scrollTop + i));
  }

  // set phases
  for (var i = 0; i < items.length; i++) {
    items[i].style.left = items[i].basicLeft + phases[i % 5] + 'px';
  }
// .. code
```

* `requestAnimationFrame` for running `updatePositions` when the scrolling event fires. This optimizes animations to run in a single paint cycle.

```js
window.addEventListener('scroll', function() {
  window.requestAnimationFrame(updatePositions);
});
```

* Reduced number of pizzas at DOM load to 32 as that also filled the screen completely. Also moved fetching of `movingPizzas1` DOM element outside the loop.

```js
document.addEventListener('DOMContentLoaded', function() {
  var cols = 8;
  var s = 256;
  var movingPizzas1 = document.getElementById("movingPizzas1");
  // ^^ moved out of loop to computer once
  for (var i = 0; i < 32; i++) {
    var elem = document.createElement('img');
    elem.className = 'mover';
    elem.src = "images/pizza.png";
    elem.style.height = "100px";
    elem.style.width = "73.333px";
    elem.basicLeft = (i % cols) * s;
    elem.style.top = (Math.floor(i / cols) * s) + 'px';
    movingPizzas1.appendChild(elem);
  }
  updatePositions();
});
```


## Part 3: Optimize time to resize pizza to be less under 5 milliseconds

Resizing pizzas takes around 20ms or so time right now. To optimize it, we edit the `changePizzaSizes` function.
The following is the final form of that function.

```js
// Iterates through pizza elements on the page and changes their widths
  function changePizzaSizes(size) {
    var elements = document.querySelectorAll(".randomPizzaContainer");
    var dx = determineDx(elements[0], size);
    var newwidth = (elements[0].offsetWidth + dx) + 'px';
    for (var i = 0; i < elements.length; i++) {
      elements[i].style.width = newwidth;
    }
  }
```

### Optimizations

* Rather than querying `randomPizzaContainer` everytime, queried it once and stored in a variable called `elements`.
* There is no need to determine dx and calculate new width for every pizza since images for all pizza is same. So calculate that for only one pizza and so move that out of the loop.
* After these optimizations, average time to resize pizzas is less than 1.2ms.
