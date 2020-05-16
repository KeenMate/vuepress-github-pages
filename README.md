# Example Github Pages with Vuepress
  
  This is an example of how to deploy Github Pages written in Vuepress.
  
## Structure
- We have two branches
  - **master** branch with *docs* folder that contains Vuepress configuration and markdown files
  - **gh-pages** branch that contains output of Vuepress build process
    - The name **gh-pages** is mandatory for Github to pick up the branch as the one for Github Pages in your repository settings
- We also have an deployment script defined for *Github Actions* that runs with every push to master branch and compiles *docs* with Vuepress

## How to create an empty *gh-pages* branch
 - Script below create a new local branch called *gh-pages* from your *master* branch
 - Removes everything 
 - Makes an empty commit
 - Pushes the empty branch to the origin
```
git checkout --orphan gh-pages
git rm -rf .
git commit --allow-empty -m "New empty branch"
git push -u origin gh-pages
```

## Compilation and deployment process
 - In principle CD/CI pipeline does three steps
   - Pulls master branch
   - Compiles Vuepress documentation
   - Initialize git in *docs/.vuepress/dist* and because it is an ignored folder it becomes a new repository
   - Makes a commit of changes in *docs/.vuepress/dist* folder and pushes it to the root of *gh-pages* branch
 - Compilation expects package.json in the root folder of this repository but it could be place in *docs* folder as well
 - *package.json* has defined Vuepress as devDependency so it need to be installed with npm i
   - global installation of Vuepress did not work
 - *package.json* also contains run configuration build **"docs:build": "vuepress build docs"** that is used to build the documentation
 - deployment script contains a step using package called [jenkey2011/vuepress-deploy](https://github.com/jenkey2011/vuepress-deploy)
   - this package does all the mentioned git steps and compilation for you but you can take the code and put it directly into your pipeline if you need
   
   :warning: **It might take several seconds (or tens of seconds) for Github Pages to pickup the change in *gh-pages* branch. Few refreshes of your browser will be neded.** :warning:
   
 ## vuepress-deploy configuration
 
 ```
  - name: vuepress-deploy
      uses: jenkey2011/vuepress-deploy@master
      env:
        ACCESS_TOKEN: ${{ secrets.ACCESS_TOKEN }} - access token has to be created by someone who has access to the repository in you Profile -> Settings -> Developer settings and stored as a secret into this repository
        TARGET_REPO: KeenMate/github-pages-test - change this to your repository
        TARGET_BRANCH: gh-pages - this should state the same
        BUILD_SCRIPT: npm run docs:build - if you build you code differently change this
        BUILD_DIR: docs/.vuepress/dist/ - if your Vuepress code is somewhere else change this
 ```
   
 ## vuepress configuration
  
```
module.exports = {
  title: 'Github Pages Test', - Update to your repository title
  base: '/github-pages-test/', - Update to your repository project name
  description: 'Simple yet powerful Github Pages in Vuepress', - Update to your repository description
  themeConfig: {
    repo: 'keenmate/github-pages-test', - Update to your full Github repository path
    docsDir: 'docs', - The rest change as you need
    editLinks: true,
    sidebarDepth: 1,
    sidebar: [
      '/'
    ]
  }
}
```
