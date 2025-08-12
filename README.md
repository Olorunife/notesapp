I deployed a React application in the AWS Cloud by integrating with GitHub and using AWS Amplify. With AWS Amplify, I can continuously deploy your application in the Cloud and host it on a globally available CDN.

I created a simple full-stack web application using AWS Amplify. Amplify offers a Git-based CI/CD workflow for building, deploying, and hosting single-page web applications or static sites with serverless backends.


## Set up the React environment

In a new terminal window, run:

```bash
npm create vite@latest notesapp -- --template react
cd notesapp
npm install
npm run dev
```

2. View your application
In the terminal window, choose the Local link.

Missing alt text value




Create the GitHub repository and commit code
Before you begin:

You need a GitHub account. If you don't have one, sign up here.

If you've never used GitHub on your computer, generate and add an SSH key to your account. For instructions, see Connecting to GitHub with SSH.


2. Create a repository
In the Start a new repository section, make the following selections:

For Repository name, enter notesapp, and choose the Public radio button.

Then select, Create a new repository.




3. Push the new repo
Open a new terminal window, navigate to your app's root folder (notesapp), and run the following commands to initialize a git and push the application to the new GitHub repo:

Note: Replace the SSH GitHub URL in the command with your SSH GitHub URL.

```
git init
git add .
git commit -m "first commit"
git remote add origin git@github.com:<your-username>/notesapp.git 
git branch -M main
git push -u origin main
```
Missing alt text value

Install the Amplify Packages
1. Configure your local repository
Open a new terminal window, navigate to your app's root folder (notesapp), and run the following command:

npm create amplify@latest -y
Missing alt text value
2. Review the Amplify project structure
Running the previous command will scaffold a lightweight Amplify project in the app’s directory.

Missing alt text value
3. Push your changes to GitHub
In your open terminal window, run the following commands to push the changes to GitHub:

```
git add .
git commit -m 'installing amplify'
git push origin main
 ```

Missing alt text value
4. Deploy your app with AWS Amplify
In this step, you will connect the GitHub repository you just created to AWS Amplify. This will enable you to build, deploy, and host your app on AWS.

1. Create the Amplify App
Sign in to the AWS Management console in a new browser window, and open the AWS Amplify console at https://console.aws.amazon.com/amplify/apps.

Choose Create new app.

Missing alt text value
2. Connect to your GitHub repository
On the Start building with Amplify page, for Deploy your app, select GitHub, and select Next.

Missing alt text value
3. Authorize and select your respository
When prompted, authenticate with GitHub. You will be automatically redirected back to the Amplify console. Choose the repository and main branch you created earlier. Then, select Next.

Missing alt text value
4. Configure build settings
Leave the default build settings and select Next.

Missing alt text value
5. Deploy your application
Review the inputs selected, and choose Save and deploy.

Missing alt text value
6. Verify your deployment
AWS Amplify will now build your source code and deploy your app at https://...amplifyapp.com, and on every git push your deployment instance will update. It may take up to 5 minutes to deploy your app.

Once the build completes, select the Visit deployed URL button to see your web app up and running live.

Missing alt text value
5. Automatically deploy code changes
In this step, you will make some changes to the code using your text editor and push the changes to the main branch of your app.

1. Update your application code
On your local machine, navigate to the notesapp/src/App.jsx file, and update it with the following code. Then, save the file.

```
import reactLogo from "./assets/react.svg";
import "./App.css";
function App() {
  return (
    <div className="App">
      <header className="App-header">
        <img src={reactLogo} className="logo react" alt="React logo" />
        <h1>Hello from Amplify</h1>
      </header>
    </div>
  );
}
export default App;
```


Missing alt text value
2. Push your code changes
In your terminal window, run the following command to push the changes to GitHub:

```
git add .
git commit -m 'changes for amplify'
git push origin main
 ```

Missing alt text value
3. Update your deployed application
AWS Amplify will now build your source code and deploy your app.

Missing alt text value
4. View your updated application
Navigate back to the Amplify console, and select the Visit deployed URL button to view your updated app.

Missing alt text value


Set up Amplify Auth
The app uses email as the default login mechanism. When the users sign up, they receive a verification email.

1. Set auth resource
By default, your auth resource is configured as shown inside the notesapp/amplify/auth/resource.ts file. For this tutorial, keep the default auth set up as is.

Missing alt text value
Set up Amplify Data
The app you will be building is a Notes app that will allow users to create, delete, and list notes. This example app will help you learn how to build many popular types of CRUD+L (create, read, update, delete, and list) applications.

1. Update authorization rule
On your local machine, navigate to the notesapp/amplify/data/resource.ts file and update it with the following code. Then, save the file.

The following updated code uses a per-owner authorization rule allow.owner() to restrict the note record’s access to the owner of the record. 

Amplify will automatically add an owner: a.string() field to each note which contains the note owner's identity information upon record creation.

 
```
import { type ClientSchema, a, defineData } from '@aws-amplify/backend';
const schema = a.schema({  
Note: a
    .model({
      name:a.string(),
      description: a.string(),
      image: a.string(),
    })
    .authorization((allow) => [allow.owner()]),
});
export type Schema = ClientSchema<typeof schema>;
export const data = defineData({
  schema,
  authorizationModes: {
    defaultAuthorizationMode: 'userPool',
  },
});
```

File directory view of a project named "notesapp," with the "resource.ts" file under the "data" folder highlighted in red.
Set up Amplify Storage
1. Create a storage folder
On your local machine, navigate to the notesapp/amplify folder, and create a new folder named storage, and then create a file named resource.ts inside of the new storage folder.

File directory structure of a "notesapp" project, with the "storage" folder and its "resource.ts" file highlighted in red.
2. Configure a storage resource for your app
Update the amplify/storage/resource.ts file with the following code to configure a storage resource for your app. Then, save the file.

The updated code will set up the access so that only the person who uploads the image can access. The code will use the entity_id as a reserved token that will be replaced with the users' identifier when the file is being uploaded. 

```import { defineStorage } from "@aws-amplify/backend";export const storage = defineStorage({  name: "amplifyNotesDrive",  access: (allow) => ({    "media/{entity_id}/*": [      allow.entity("identity").to(["read", "write", "delete"]),    ],  }),});```

Deploy Amplify Cloud sandbox
1. Import backend definitions
On your local machine, navigate to the amplify/backend.ts file, and update it with the following code. Then, save the file.

The following code will import the auth, data, and storage backend definitions:

 
```
import { defineBackend } from '@aws-amplify/backend';
import { auth } from './auth/resource';
import { data } from './data/resource';
import { storage } from './storage/resource';
/**
 * @see https://docs.amplify.aws/react/build-a-backend/ to add storage, functions, and more
 */
defineBackend({
  auth,
  data,
  storage
});
```

Missing alt text value
2. Start sandbox environment
To start your own personal cloud sandbox environment that provides an isolated development space, in a new terminal window, run the following command in your apps root folder:

npx ampx sandbox
The sandbox allows you to rapidly build, test, and iterate on a fullstack app. Each developer on your team can use their own disposable sandbox environment connected to cloud resources. You can learn more about it here.

3. Confirm deployment
Once the cloud sandbox has been fully deployed, your terminal will display a confirmation message.

Missing alt text value
3. Verify JSON file was added
The amplify_outputs.json file will be generated and added to your project. 

Missing alt text value
Conclusion
You used Amplify to configure auth, data, and storage resources. You also started your own cloud sandbox environment.



<<<<<<< HEAD
# React + Vite

This template provides a minimal setup to get React working in Vite with HMR and some ESLint rules.

Currently, two official plugins are available:

- [@vitejs/plugin-react](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react) uses [Babel](https://babeljs.io/) for Fast Refresh
- [@vitejs/plugin-react-swc](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react-swc) uses [SWC](https://swc.rs/) for Fast Refresh

## Expanding the ESLint configuration

If you are developing a production application, we recommend using TypeScript with type-aware lint rules enabled. Check out the [TS template](https://github.com/vitejs/vite/tree/main/packages/create-vite/template-react-ts) for information on how to integrate TypeScript and [typescript-eslint](https://typescript-eslint.io) in your project.
=======
# notesapp
>>>>>>> 24a8787cb95099d67e26062b58753cdbe8057066

