# FDP_hugo

The [FAIR Data Pipeline website](https://www.fairdatapipeline.org/) contains information related to the FAIR Data Pipeline, its registry, command line tool and the language-specific APIs as well as various other technical documents associated with this [SCRC](https://scottishcovidresponse.github.io) project.

* [FAIRDataPipeline/FDP_hugo](https://github.com/FAIRDataPipeline/FDP_hugo) (this repo) contains Hugo source files 
  * When pushes are made to this repository, a GitHub actions workflow automatically deploys source files to [FAIRDataPipeline/fairdatapipeline.github.io](https://github.com/FAIRDataPipeline/fairdatapipeline.github.io)

* [FairDataPipeline/fairdatapipeline.github.io](https://github.com/FAIRDataPipeline/fairdatapipeline.github.io) contains website source files.

* [RyanJField/hugo-book](https://github.com/RyanJField/hugo-book) contains Hugo theme files
  * This theme is based on the Hugo [Book](https://github.com/alex-shpak/hugo-book) theme, with the search bar from the Jekyll [Just the Docs](https://github.com/pmarsceill/just-the-docs) theme, and some extra tweaks
  * It is included as a submodule

## How to edit the site

It is not necessary to build the site in order to contribute changes, but if you want to see how these will be rendered, you will need to cary out the folowing steps:

1. [Install `hugo extended`](https://gohugo.io/getting-started/installing/).
2. Clone this repository `git clone https://github.com/FAIRDataPipeline/FDP_hugo.git`.
3. Install the theme using the submodule: `git submodule update --init --recursive`.
4. Information regarding the directory structure of the website and how make edits to to the template (mostly in markdown) can be found [here](https://gohugo.io/getting-started).
5. In the folder that contains this repository run the command `hugo server`.
6. The website should build locally on http://localhost:1313/.

After pushing to Github, source files should automatically be generated and the website should build online. Alternatively, click on the Edit this page link (at the bottom of each page, next to the pencil) to edit an existing page. or email a markdown / word document and we'll put it up for you.
