[![Netlify Status](https://api.netlify.com/api/v1/badges/d380d91c-c330-4cc0-9087-3cebed2c3d10/deploy-status)](https://app.netlify.com/sites/ppmedenhaag/deploys)

# ppmedenhaag-website

Static web page hosted for [PPME Den Haag Website](https://www.ppmedenhaag.nl).
Built with [Hugo](https://www.gohugo.io) based on [Educenter](https://github.com/themefisher/educenter-hugo) theme.


## Local Installation and Development

You will only need hugo extended version to be able to run the website locally on your machine. You will need git software to be able to contribute to ppme-website GitHub repository.


### Git Toolings

1. If not already, please create GitHub account from https://github.com/signup.
2. Please download git based on your OS from https://git-scm.com/downloads and install it with all the default options.
3. It might also help to install [GitHub Desktop](https://desktop.github.com/). If you install GitHub desktop, you can proceed with authentication to GitHub described [here](https://docs.github.com/en/desktop/installing-and-configuring-github-desktop/installing-and-authenticating-to-github-desktop/authenticating-to-github).
4. Alternatively if you prefer git from command line, follow the steps for example from [here](https://mgimond.github.io/Colby-summer-git-workshop-2021/authenticating-with-github.html).

### Editor (VS Code or alternatives)

We can modify the website content via text editor, but a good code/development tools like VS Code will improve our productivity in adding or revising the website content. You can go download VS Code from [here](https://code.visualstudio.com/download) and install it locally on your machine. 

### Hugo (Extended)
Download and install latest **hugo_extended** version from https://github.com/gohugoio/hugo/releases. The extended version is required to compile SCSS.
Make sure the hugo extended binary is accessible from console with the following command:
`hugo version`

It should display Hugo version with +extended signature.

### Clone the ppme-website repository

![image](https://user-images.githubusercontent.com/101281822/163545999-942baf13-f2f5-46ba-aee8-325436a86071.png)

If you use GitHub Desktop, you can follow the steps documented [here](https://docs.github.com/en/desktop/contributing-and-collaborating-using-github-desktop/adding-and-cloning-repositories/cloning-a-repository-from-github-to-github-desktop).

Alternatively if you use git command line/cli, you can clone the repository and execute Hugo command to serve the content locally.

```
git clone https://github.com/r0stiars0/ppmedenhaag-website.git
cd ppmedenhaag-website
```

### Running Hugo Locally

In your command line, go to the ppmedenhaag-website directory, and run the following command.
```
hugo server
```

By default, hugo will run http process and serve ppmedenhaag-website in http://localhost:1313/




