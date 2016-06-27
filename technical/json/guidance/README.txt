
Directions to get the repository up and running. This assumes the repo is
checked out as $root.

1. Install the packages that the NIEM.GitHub.io website requires, which are
   installed as submodules.

   $ cd $root
   $ git submodule init
   $ git submodule update

2. Install the Ruby packages that GitHub Pages uses:

   $ sudo ruby gem install --verbose github-pages

3. Run jekyll

   $ cd $root
   $ jekyll serve

4. Point your browser to http://127.0.0.1:4000/technical/json/guidance/

