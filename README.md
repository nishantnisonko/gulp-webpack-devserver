# gulp-webpack-devserver
boiler plate configuration having following features  A task manager, A development server, A module bundler

<img class="  wp-image-1680 alignleft" src="http://www.knowstack.com/wp-content/uploads/2015/07/Unknown-2.jpeg" alt="Unknown-2" width="177" height="177" /> 

<img class="  wp-image-1679 alignleft" src="http://www.knowstack.com/wp-content/uploads/2015/07/Unknown-300x162.jpeg" alt="Unknown" width="285" height="154" />


In a typical frontend Development environment, it is typical for developers to have a boiler plate configuration to have a ready to start coding project setup, which would include the following features already built in for swift and clean build and testing
<ol>
	<li>A task manager with commonly used build tasks</li>
	<li>A development server which is spinned off once you start development</li>
	<li>A module bundler to convert your modular source code to a browser servable version.</li>
</ol>
In this setup i would be using the following tech stack
<ul>
	<li>Gulp as the task manager</li>
	<li>Webpack as the module bundler and as the dev server</li>
	<li>Node package manager</li>
</ul>
Here is a detailed rundown
<h5>1.Install node</h5>
Go to http://nodejs.org

Node installation comes with npm (node package manager)
<pre style="padding-left: 30px;"><code>$ node -v
v0.12.7
$ npm -v
2.11.3</code></pre>
<h5>2. Setup a new npm project</h5>
<pre style="padding-left: 30px;"><code>$ mkdir MyAwesomeProject
$ cd MyAwesomeProject
$ npm init</code></pre>
<h5>3. Install gulp and its utils/tasks</h5>
<pre style="padding-left: 30px;"><code>$ npm install gulp --save-dev
$ npm install gulp-util --save-dev
$ npm install gulp-uglify --save-dev
$ npm install gulp-concat --save-dev
$ npm install gulp-sourcemaps --save-dev</code></pre>
<h5>4. Install webpack and webpack dev server</h5>
Before installing Webpack lets understand<strong> what is Webpack</strong>, it's a module bundler which has the capability to transform your code which is written in any of the prevalent modular design patterns e.g AMD, CommonJS, ES6 import, RequireJS into plain modules in javascript which when attached to a html, will seamlessly work. <img class="  wp-image-1678 aligncenter" src="http://www.knowstack.com/wp-content/uploads/2015/07/Unknown-1-300x150.jpeg" alt="Unknown-1" width="550" height="275" />

Webpack is the more powerful evil cousin of <strong>Browserify. </strong>Additionally, it provides an option to spinoff a dev server in your desired port along with the capabilities of watching resources.
<pre style="padding-left: 30px;"><code>$ npm install webpack --save-dev
$ npm install webpack-dev-server --save-dev
$ npm install webpack-stream --save-dev //seamlessly integrate webpack with gulp</code></pre>
<h5>5. Create the webpack config file</h5>
webpack.config.js

<strong>entry</strong> config is where you tell webpack where the flow starts in your webpage.

<strong>output </strong>config tells webpack to produce the transformed script in the desired path.

<strong>extensions</strong> is a config which tells webpack to automatically add matching extensions in the matching dependencies invoked in the source code. You can also give optional configs like cache, debug and devtool.
<pre style="padding-left: 30px;"><code>
 var path = require('path');
 module.exports = {
 cache: true,
 debug: true,
 devtool: 'eval',
 entry: './src/app.js',
 output: {
 path: path.join(__dirname, "build"),
 filename: 'build.min.js'
 },
 resolve: {
 extensions: ['', '.js', '.json', '.coffee']
 }
 };</code></pre>
<h5>6. Create the Gulpfile.js</h5>
Gulpfile.js is the place holder where you will define all your common build/ lint tasks and combine it with webpack.

Get all the dependencies:
<code></code>
<pre style="padding-left: 30px;"><code>var gulp = require('gulp');
 var concat = require('gulp-concat');
 var uglify = require('gulp-uglify');
 var sourcemaps = require('gulp-sourcemaps');
 var gutil = require("gulp-util");
 var webpack = require("webpack");
 var WebpackDevServer = require("webpack-dev-server");
 var webpackConfig = require("./webpack.config.js");
 var stream = require('webpack-stream');</code></pre>
<h5>Here is what the project structure looking like:</h5>
<pre style="padding-left: 30px;"><code>├── index.html
 ├── package.json
 ├── Gulpfile.js
 ├── webpack.config.js
 ├── src/**/*.js
 └── app.js
 └── module1.js
 └── module2.js
 ├── images (all images stored here)
 ├── js (all js files stored here)
 └── styles (all .less/.css files stored here)</code></pre>
<h5>7. Lets add some juice to our gulp (pun intended!)</h5>
<h5 style="padding-left: 30px;">7.1 First lets add the webpack task</h5>
<pre style="padding-left: 60px;"><code>var path = {
 HTML: 'src/index.html',
 ALL: ['src/**/*.jsx', 'src/**/*.js'],
 MINIFIED_OUT: 'build.min.js',
 DEST_SRC: 'dist/src',
 DEST_BUILD: 'dist/build',
 DEST: 'dist'
 };
 gulp.task('webpack', [], function() {
 return gulp.src(path.ALL) // gulp looks for all source files under specified path
 .pipe(sourcemaps.init()) // creates a source map which would be very helpful for debugging by maintaining the actual source code structure
 .pipe(stream(webpackConfig)) // blend in the webpack config into the source files
 .pipe(uglify())// minifies the code for better compression
 .pipe(sourcemaps.write())
 .pipe(gulp.dest(path.DEST_BUILD));
 });</code></pre>
<h5 style="padding-left: 30px;">7.2 Spinoff the webpack dev server task</h5>
<pre style="padding-left: 60px;"><code> gulp.task("webpack-dev-server", function(callback) {
 // modify some webpack config options
 var myConfig = Object.create(webpackConfig);
 myConfig.devtool = "eval";
 myConfig.debug = true;</code></pre>
<pre style="padding-left: 60px;"><code> // Start a webpack-dev-server
 new WebpackDevServer(webpack(myConfig), {
 publicPath: "/" + myConfig.output.publicPath,
 stats: {
 colors: true
 }
 }).listen(8080, "localhost", function(err) {
 if (err) throw new gutil.PluginError("webpack-dev-server", err);
 gutil.log("[webpack-dev-server]", "http://localhost:8080/webpack-dev-server/index.html");
 });
 });</code></pre>
<h5 style="padding-left: 30px;">7.3 Now let's setup the watcher in Gulp which shall look for all changes in the statc resources and invoke the webpack instantly.</h5>
<pre style="padding-left: 60px;"><code> gulp.task('watch', function() {
 gulp.watch(path.ALL, ['webpack']);
 });</code></pre>
<h5 style="padding-left: 30px;">7.4 Finally setup the default gulp task which starts the webpack dev server and initiates the watch</h5>
<pre style="padding-left: 60px;"><code>gulp.task('default', ['webpack-dev-server', 'watch']);</code></pre>
<h5 style="padding-left: 30px;">7.5 Now lets do a run</h5>
<pre style="padding-left: 60px;"><code>$ gulp
 [14:56:10] Using gulpfile <span style="color: #800080;">~/Desktop/MyAwesomeProject/Gulpfile.js</span>
 [14:56:10] Starting '<span style="color: #33cccc;">webpack-dev-server</span>'...
 [14:56:10] Starting '<span style="color: #33cccc;">watch</span>'...
 [14:56:10] Finished '<span style="color: #33cccc;">watch</span>' after <span style="color: #800080;">9.52 ms</span>
 [14:56:10] [webpack-dev-server] http://localhost:8080/webpack-dev-server/index.html
 Hash: <span style="color: #800080;">154fd7d206186335ec46</span>
 Version: webpack <span style="color: #800080;">1.10.5</span>
 Time: <span style="color: #800080;">252ms</span>
 <span style="color: #800080;">Asset    Size  Chunks             Chunk Names</span>
 <span style="color: #00ff00;">build.min.js</span>  281 kB       0  <span style="color: #00ff00;">[emitted] </span> main
 chunk    {0} <span style="color: #00ff00;">build.min.js</span> (main) 248 kB <span style="color: #00ff00;">[rendered]</span>
 [0] <span style="color: #800080;">./src/app.js</span> 374 bytes {0} <span style="color: #00ff00;">[built]</span>
 [1] <span style="color: #800080;">./~/jquery/dist/jquery.js</span> 248 kB {0} <span style="color: #00ff00;">[built]</span>
 webpack: bundle is now VALID.</code></pre>
&nbsp;

&nbsp;

&nbsp;

References:
<ul>
	<li>http://gulpjs.com</li>
	<li>http://webpack.github.io</li>
</ul>
