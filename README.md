# symfony-tailwind

First step is to set up a new Symfony project that we will call "symfony-tailwind". To do so, we use the classic Symfony CLI command.

<code># symfony new tailwind --full</code>

## you can launch the Symfony server to check that everything is OK.

<code># symfony server:start -d</code>

And you check in a web browser that everything is OK. In my case at http://127.0.0.1:8002.
![alt text](https://github.com/Mawlid/symfony-tailwind/blob/master/assets/images/screenshot.png?raw=true)

### Installation of Webpack Encore

Second step is now, the installation of Webpack Encore.

<code># composer require symfony/webpack-encore-bundle</code>

Once Webpack Encore is in place, let's ask npm to download the basic Webpack Encore dependencies. (you can also use "yarn".)

<code># npm install</code>

the Symfony project is now able to use Webpack to compile JS, and in this case CSS.

### Installation of Tailwind CSS and PurgeCSS

Now It's time to install Tailwind and PurgeCSS. As we are using it in a "Webpack" context, we add and modify a bit the commands suggested in the Tailwind documentation.

<code># npm install -D tailwindcss postcss-loader purgecss-webpack-plugin glob-all path autoprefixer</code>

### Configuration file

To make our Tailwind implementation work, you need to modify a few files.
First create a postcss.config.js file (at the root of your project).

and past the flowing code:

```
module.exports = {
    plugins: [
        require('tailwindcss'),
    ],
};
```
then: modify your webpack.config.js file, by adding at the beginning of the file the references to the libraries you will use.


```
var Encore = require('@symfony/webpack-encore');

const PurgeCssPlugin = require('purgecss-webpack-plugin');

const glob = require('glob-all');

const path = require('path');
```

Still in the webpack.config.js file, you should enable the use of the PostCSS loader as described in the Webpack Encore documentation.

```.enablePostCssLoader() ```

you also need to import the stylesheets into our CSS file, which is the goal when using a CSS framework ;-). To do so, let's add these few lines to our assets/styles/app.css file.

```
@import "tailwindcss/base";
 
@import "tailwindcss/components";
 
@import "tailwindcss/utilities";
```

At this point you should be able to run a first build to verify that your configuration works.

<code># npm run build</code>


Depending on the case, you may get the message "Error: PostCSS plugin tailwindcss requires PostCSS 8. To fix the problem, Tailwind offers you the solution in the official documentation: [https://tailwindcss.com/docs/installation#post-css-7-compatibility-build]


To validate the proper functioning of your installation, you should set up a test page that will use the possibilities of Tailwind CSS.

don't forget to modify the "templates/base.html.twig" file to use the files compiled by Webpack Encore.

```
{{ encore_entry_link_tags('app') }}

{{ encore_entry_script_tags('app') }}
```

add these few lines in your webpack.config.js file. 

this will minimize drastically the size of your css file

```
if (Encore.isProduction()) {
    Encore.addPlugin(new PurgeCssPlugin({
          paths: glob.sync([
              path.join(__dirname, 'templates/**/*.html.twig')
          ]),
          defaultExtractor: (content) => {
              return content.match(/[\w-/:]+(?<!:)/g) || [];
          }
    }));
}
```
