# DEPLOY6_FE
<h1 align=center>Deployment 6</h1>

Welcome to deployment 6, for this deployment you will need to follow the directions in the deployment6.pdf.    

- Be sure to include the following below in your pull request: 

***Requirements:*** 
- [x]Document issues and anything you decided to do different..
- [x]Generate a failed and passed test.
- [x]Take a screenshots of your failed and passed test.
- [x]Locate and upload cypress's screenshot and video of the headless browser test. 

👉Link to deployment instructions: [here](https://github.com/kura-labs-org/DEPLOY6_FE/blob/main/Deployment%236.pdf)  

![image](https://i.morioh.com/210507/ac11056f.webp)

This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

## Available Scripts

In the project directory, you can run:

### `npm start`

Runs the app in the development mode.<br>
Open [http://localhost:3000](http://localhost:3000) to view it in the browser.

The page will reload if you make edits.<br>
You will also see any lint errors in the console.

### `npm test`

Launches the test runner in the interactive watch mode.<br>
See the section about [running tests](https://facebook.github.io/create-react-app/docs/running-tests) for more information.

### `npm run build`

Builds the app for production to the `build` folder.<br>
It correctly bundles React in production mode and optimizes the build for the best performance.

The build is minified and the filenames include the hashes.<br>
Your app is ready to be deployed!

See the section about [deployment](https://facebook.github.io/create-react-app/docs/deployment) for more information.

### `npm run eject`

**Note: this is a one-way operation. Once you `eject`, you can’t go back!**

If you aren’t satisfied with the build tool and configuration choices, you can `eject` at any time. This command will remove the single build dependency from your project.

Instead, it will copy all the configuration files and the transitive dependencies (Webpack, Babel, ESLint, etc) right into your project so you have full control over them. All of the commands except `eject` will still work, but they will point to the copied scripts so you can tweak them. At this point you’re on your own.

You don’t have to ever use `eject`. The curated feature set is suitable for small and middle deployments, and you shouldn’t feel obligated to use this feature. However we understand that this tool wouldn’t be useful if you couldn’t customize it when you are ready for it.

## Learn More

You can learn more in the [Create React App documentation](https://facebook.github.io/create-react-app/docs/getting-started).

To learn React, check out the [React documentation](https://reactjs.org/).

### Code Splitting

This section has moved here: https://facebook.github.io/create-react-app/docs/code-splitting

### Analyzing the Bundle Size

This section has moved here: https://facebook.github.io/create-react-app/docs/analyzing-the-bundle-size

### Making a Progressive Web App

This section has moved here: https://facebook.github.io/create-react-app/docs/making-a-progressive-web-app

### Advanced Configuration

This section has moved here: https://facebook.github.io/create-react-app/docs/advanced-configuration

### Deployment

This section has moved here: https://facebook.github.io/create-react-app/docs/deployment

### `npm run build` fails to minify

This section has moved here: https://facebook.github.io/create-react-app/docs/troubleshooting#npm-run-build-fails-to-minify

<h1>Instructions</h1>
<h2>Infrastrucsture</h2>
Used my aws VPC deploy script to deploy a new production/testing environment with 4 subnets, 2 public and 2 private.
First Ec2
t2.micro Amazon ami
vpc public subnet 1a
SG port 22 8080 set as SG1

Second Ec2
t2.micro ubuntu 20
vpc public subnet 1a
SG port 22 and allow SG Group 1
Bootstrap

``
sudo apt update -y
sudo apt upgrade -y
sudo apt install default-jre git npm nodejs -y
``

Third Ec2
t2.micro ubuntu 20
vpc public subnet 1a
SG port 22 and allow SG Group1
Bootstrap
``
sudo apt update -y
sudo apt upgrade -y
sudo apt install default-jre git nodejs npm maven libgtk2.0-0 libgtk-3-0 libgbm-dev libnotify-dev libgconf-2-4 libnss3 libxss1 libasound2 xauth xvfb -y
``

<h2>EC21</h2>
Install Jenkins, use recommended plugins, followed up additional plugins Maven, nodejs, and EC2 plugin
Set up multibranch pipeline to use a git account that can be added using Personal Access token
https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token
Access the git that has the kura repo and edit the jenkinsfiles to include an & after the serve build and also the label to refer stage 2 to the worker2 and stage 1 for worker1

Set up agents with the following tag. (worker1) for Second EC2 and (worker2) for Third EC2
For the settings, use as much as possible as this will be manually overrided using the tags in teh Jenkinsfile.
Add accounts with credentials based off the SSH key plaintext of the private key associated with each respective EC2 and use non-verifying strategy.
For directory, it will be ``/home/ubuntu/jenkins`` since it is referencing a ubuntu default user server.
Check that the jenkins are online, if not. there may be something off.

<h2>Additional Changes needed to be made</h2>
Change the test file in the integration folder to the respective IP if worker2.

Test should now run and succeed.

<h2>How to cause failure</h2>


<h1>Documentation</h1>
There were several problems that I ran into to deplying:
Jenkins`error ERROR: Error cloning remote repo 'origin'`
Solution: needed to install git on main server

`npm WARN saveError ENOENT: no such file or directory, open '/home/ubuntu/jenkins/workspace/reactbuild_main/package.json'
npm WARN notsup Unsupported engine for mocha@9.1.2: wanted: {"node":">= 12.0.0"} (current: {"node":"10.19.0","npm":"6.14.4"})`
Solution: Manually installed mocha latest version

`test.spec.js was missing.`
Solution: checked, files and was present. needed to rerun build and check directories. found directory.

`cound not find the cypress.json file as the script kept looking in the wrong place, so I found the cypress file in the sub directory and placed that one level up where the script was running.`
Solution:place cypress in the location that the script was looking at

All of these problems still persisted. Spoke with a colleague to see their repo. It turns out the original kura_labs_repo files should be placed at the root, which was causing the issues of location and dependency issues since the script and subsequent scripts used relative pathways.
Solution: Move kura repo files to the root. That fixed most issues.

``Time out on server in stage 2``
Solution: Change the server pathway in test integrations js file to your respective worker1's IP
