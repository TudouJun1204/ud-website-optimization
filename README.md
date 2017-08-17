# Website Performance Optimization portfolio project

http://aviaryan.github.io/ud-website-optimization/

Udacity FEND project

The aim of this project is to optimize the critical rendering path and make the index page render as quickly as possible by applying the techniques picked up in the [Critical Rendering Path course](https://www.udacity.com/course/ud884).
Also we had to optimize FPS in pizza.html.

To get started, check out the repository and inspect the code.

## Getting started

### Part 1: Optimize PageSpeed Insights score for index.html

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

#### Optimizations

The following optimizations were done.

1. Inline `style.css` in the index.html file.
2. Use a `media=print` query on print.css so that it is only rendered when printing.
3. Move google analytics to the end of the `body` tag and made its script load async.
4. Use [WebFont](https://github.com/typekit/webfontloader) to load fonts so that it doesn't block rendering.
5. Compress `profilepic.jpg` to `profilepic_compressed.jpg` without sacrificing quality using ImageOptim.
6. Reduce dimensions of `pizerria.jpg` to `pizzeria_small.jpg` because width of that image on page is only 100px.


### Part 2: Optimize Frames per Second in pizza.html

In this, we had to modify `views/js/main.js`. FPS Counter/HUD Display useful in Chrome developer tools has been described here: [Chrome Dev Tools tips-and-tricks](https://developer.chrome.com/devtools/docs/tips-and-tricks).


