[![Netlify Status](https://api.netlify.com/api/v1/badges/d380d91c-c330-4cc0-9087-3cebed2c3d10/deploy-status)](https://app.netlify.com/sites/ppmedenhaag/deploys)

# ppmedenhaag-website

Static web page hosted for [PPME Den Haag Website](https://www.ppmedenhaag.nl).
Built with [Hugo](https://www.gohugo.io) based on [Educenter](https://github.com/themefisher/educenter-hugo) theme.


## Local Installation and Development

Download and install latest **hugo_extended** version from https://github.com/gohugoio/hugo/releases. The extended version is required to compile SCSS.
Make sure the hugo extended binary is accessible from console with the following command:
`hugo version`

It should display Hugo version with +extended signature.

Next, clone the repository and execute Hugo command to serve the content locally.

```
git clone https://github.com/r0stiars0/ppmedenhaag-website.git
cd ppmedenhaag-website
hugo server
```