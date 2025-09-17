# Vercel Hobby Collaboration Github Actions Workflow

## Introduction 
Are you tired of seeing ```All checks have failed. Vercel - Git author [username] must have access to the project on Vercel to create deployments.```? 

You do not have to see that anymore. I created this GitHub Actions workflow guide that allows you to collaborate with your team on the Vercel Hobby plan. It automatically deploys your changes for any collaborator on the repository!   

This guide is only for educational purposes. I just want to people to learn how to setup GitHub Actions, save  time, and save money :) 

## Context  
Vercel has disabled collaboration on the Hobby plan. This means that only the repository owner is able to commit and deploy the changes to a Vercel Hobby private project. Now users have to be invited to a team and pay U$20/month per user to collaborate. 

I love Vercel and their services (in fact, I use Vercel for everything and have bought many domains using Vercel), but there is no way that I'm paying U$20/month for each user on a project. I find that really crazy. If I have 5 people working on a repository, it'll be U$100/month for one team. Say I have many teams: that might cost thousands of dollars a month. 

This is just the better (and free) alternative if you're just trying to collaborate on simple projects. 

# NEW WORK AROUND (SINCE AUGUST 2025) 

## How to setup the workflow? (For production environments)? 
1. Create a ```.github``` folder in the root folder of your application 
2. Create a ```workflows``` folder inside the ```.github``` folder 
3. Inside the ```github/workflows``` folder, create a ```production.yaml``` file 
4. Copy the following code snippet and paste it to the ```production.yaml``` file, then commit your changes 
```
name: Vercel Production Deployment

on:
  push:
    branches:
      - main 

jobs:
  Deploy-Production:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Trigger Vercel Deploy Hook
        run: |
          curl -X POST ${{ secrets.VERCEL_DEPLOY_HOOK }}

```
5. View the ```Actions``` tab on the GitHub repository to see the workflow run (it will fail initially but don't worry it's expected!) 
6. The workflow run has failed because it's missing the environment variables ```VERCEL_DEPLOY_HOOK```. We need to add the environment variable now. 
7. ```VERCEL_DEPLOY_HOOK``` is the your Deploy Hook 
   1. This can be found on https://vercel.com/[your-project-name] on the ```Settings``` tab > ```Git```
   2. Create a Deploy Hook and then add the branch (e.g. main)
   3. This will create a link which is your deploy hook 
8. Now that you have the environment variable, go to your GitHub repository and click ```Settings```
9. Go to ```Secrets and variables``` > ```Actions```
10. Add each repository secret ```New Repository Secret``` 
    1. Write the environment variable as the Name (e.g. VERCEL_DEPLOY_HOOK) and paste the secret on Secret  
11. Push any changes to the repository to run the workflow, and it should pass now! Now anyone who has access to your repository is able to commit and deploy to Vercel on the Hobby plan! 

---
# DEPRECATED (THIS WORKAROUND DOES NOT WORK SINCE AUGUST 2025) 

## How to setup the workflow? (For production environments)? 
1. Create a ```.github``` folder in the root folder of your application 
2. Create a ```workflows``` folder inside the ```.github``` folder 
3. Inside the ```github/workflows``` folder, create a ```production.yaml``` file 
4. Copy the following code snippet and paste it to the ```production.yaml``` file, then commit your changes 

```
name: Vercel Production Deployment
env:
  VERCEL_ORG_ID: ${{ secrets.VERCEL_ORG_ID }}
  VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}
on:
  push:
    branches:
      - main
jobs:
  Deploy-Production:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Install Vercel CLI
        run: npm install --global vercel@latest
      - name: Pull Vercel Environment Information
        run: vercel pull --yes --environment=production --token=${{ secrets.VERCEL_TOKEN }}
      - name: Build Project Artifacts
        run: vercel build --prod --token=${{ secrets.VERCEL_TOKEN }}
      - name: Deploy Project Artifacts to Vercel
        run: vercel deploy --prebuilt --prod --token=${{ secrets.VERCEL_TOKEN }}

```

5. View the ```Actions``` tab on the GitHub repository to see the workflow run (it will fail initially but don't worry it's expected!) 
6. The workflow run has failed because it's missing the environment variables ```VERCEL_ORG_ID```, ```VERCEL_PROJECT_ID```, and ```VERCEL TOKEN```. We need to add the environment variables now. 
7. ```VERCEL_ORG_ID``` is the Team ID 
   1. This can be found on https://vercel.com/[your-team-name] on the ```Settings``` tab
8. ```VERCEL_PROJECT_ID``` is the Project ID 
   1. This can be found on https://vercel.com/[your-team-name]/[your-project-name] on the ```Settings``` tab 
9. ```VERCEL_TOKEN``` is a token that allow other apps to control your whole account. Be careful! 
   1. Go to ```Account Settigns``` > ```Tokens```  
   2. Choose a specific token name like [project-name-github-actions-workflow] and select the scope as the Team that is working on the project 
   3. Choose an expiration date and click ```Create``` 
   4. Copy the token and store it somewhere temporarily (don't share it with anyone!)
10. Now that you have all the environment variables, go to your GitHub repository and click ```Settings```
11. Go to ```Secrets and variables``` > ```Actions```
12. Add each repository secret ```New Repository Secret``` 
    1. Write the environment variable as the Name (e.g. VERCEL_ORG_ID) and paste the secret on Secret  
13. Push any changes to the repository to run the workflow, and it should pass now! Now anyone who has access to your repository is able to commit and deploy to Vercel on the Hobby plan! 

## How to setup for branches (For non-production environments)?
1. Create a ```.github/workflows``` folder in the root folder of your application
2. Create a ```worfklows``` folder inside the ```.github``` folder
3. Inside the ```.github/workflows``` folder, create a ```preview.yaml``` file 
4. Copy the following code snippet and paste it to the ```preview.yaml``` file, then commit your changes 
   1. Please note that it adds ```main``` to ```branches-ignore:``` which means all branches can trigger the workflow 
   2. Configure to your needs and add your ```[branch-name]``` to a ```branch:``` if you only want the workflow to run on a specific branch 
```
name: Vercel Preview Deployment
env:
  VERCEL_ORG_ID: ${{ secrets.VERCEL_ORG_ID }}
  VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}
on:
  push:
    branches-ignore:
      - main
jobs:
  Deploy-Preview:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Install Vercel CLI
        run: npm install --global vercel@latest
      - name: Pull Vercel Environment Information
        run: vercel pull --yes --environment=preview --token=${{ secrets.VERCEL_TOKEN }}
      - name: Build Project Artifacts
        run: vercel build --token=${{ secrets.VERCEL_TOKEN }}
      - name: Deploy Project Artifacts to Vercel
        run: vercel deploy --prebuilt --token=${{ secrets.VERCEL_TOKEN }}

```
5. Follow the same steps as above for production environments  

## FAQ 
1. #### How do I create a ```.github/workflows``` folder? 
   1. **IDE:** This can be done manually by creating a ```.github``` folder and a ```workflows``` folder inside and pushing your changes 
   2. **Github.com:** Go to the repository on github.com and click ```Actions``` then ```set up a workflow yourself``` which will automatically create a ```.github/workflows``` folder. 
   
2. #### Where do I find my VERCEL_ORG_ID? 
   1. This can be found on https://vercel.com/[your-team-name] on the ```Settings``` tab
   2. It is also known as the Team ID (yes, I know it's confusing)
   3. It should look something like ```team_ABCDEFG123456```

3. #### Where do I find my VERCEL_PROJECT_ID?
   1. This can be found on https://vercel.com/[your-team-name]/[your-project-name] on the ```Settings``` tab
   2. It should look something like ```prj_ABCDEFG123456```

4. #### Where do I find my VERCEL_TOKEN? 
   1. Click your profile picture or account 
   2. Click ```Account Settings``` 
   3. Click ```Tokens``` 
   4. Choose a specific token name like [project-name-github-actions-workflow] and select the scope as the Team that is working on the project.
   5. Choose an expiration date and click ```Create```. 
   6. Copy the token and store it somewhere temporarily (don't share it with anyone!) 
   7. You can always manage and delete your tokens at any time! 

5. #### How do I add the environment variable secrets? 
   1. Open the GitHub repository and on https://github.com 
   2. Click ```Settings```>```Secrets and variables```>```Actions```
   3. Click ```New Repository Secret``` 
      1. Add each environment variable like ```VERCEL_ORG_ID``` as the name and ```team_ABCDEFG123456``` as the secret. 

6. #### Is this allowed? Doesn't Vercel want us to upgrade to the Pro plan to collaborate? 
   1. Yes, this is allowed. In fact, it is even mentioned in their own official guide (link below). 

--- 

## Collaborators 
Collaborators are welcome! Feel free to help me if I'm missing anything or if you have configuration suggestions for a specific workflow! 

## Resources 
1. https://vercel.com/guides/how-can-i-use-github-actions-with-vercel
2. https://vercel.com/docs/plans 

## Tags (ignore)
- Vercel Hobby Plan Collaboration 
- Vercel Free Plan Collaboration 
- Vercel Hobby Plan Deployment 
- Vercel Hobby Plan CI/CD 
- Vercel Hobby Plan Automatic Deployment GitHub Actions
- Git author must have access to the project on Vercel to create deployments. 
