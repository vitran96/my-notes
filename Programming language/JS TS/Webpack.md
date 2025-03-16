- Module bundler for modern JS app.
- It recursively builds a dependency graph that includes every module your app needs, then packages all of those modules into a small number of bundles.

# Definition

- Bundle: a JS file that incorporates assets:
    - That belong together.
    - Should be served to the client in response to a single file request.
    - May include JS, CSSk HTML and any other kind of file.
- Webpack examines your application source code for import statements
    - Builds a dependency graph
    - Emits one or more bundles
    - Use plugins to preprocess and minify non-JS files: TS, SASS, LES, etc...
- Configuration in webpack.config.js

# Webpack concepts

- Entry: the point where webpack should start and follow the graph of dependencies
- Output: where to bundle your application
- Loaders: Webpack only understand JS. Webpack treats every file as a module. Loaders transform files into modules as they are added to the dependency graph
- Plugins: pergorm actions and custom functionality on compilations or chunks of your bundled modules and more