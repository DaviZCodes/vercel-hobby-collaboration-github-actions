# Vercel Hobby Collaboration Github Actions Workflow

> By DaviZCodes 

## Introduction 
Are you tired of seeing ```All checks have failed. Vercel - Git author [username] must have access to the project on Vercel to create deployments.```? 
Because I for sure am! Every time I saw this, I had to manually pull the changes and commit. It started to become troublesome for large scale projects with many collaborators. 

So I created this GitHub Actions workflow guide that allows you to collaborate with your team on the Vercel Hobby plan. It automatically deploys your changes for any collaborator on the repository!   

This guide is only for educational purposes. I just want to people to learn how to setup GitHub Actions, save  time, and save money :). 

## How to setup Vercel Hobby Collaboration? (For production environments)
1. Create a ```.github``` folder in the root folder of your application 
    1. This will automatically create a ```github/workflows``` folder 
2. Inside the ```github/workflows``` folder, create a ```production.yaml``` file 
3. Copy the following code snippet and paste it to the ```production.yaml``` file. 
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
4. View Actions to see the workflow run (it will fail initially but don't worry!). 
5. The workflow run has failed because it's missing the environment variables ```VERCEL_ORG_ID```, ```VERCEL_PROJECT_ID```, and ```VERCEL TOKEN```. We need to add the environment variables now. 
6. ```VERCEL_ORG_ID``` is the Team ID 
   1. This can be found on https://vercel.com/[your-team-name] on the ```Settings``` tab
7. ```VERCEL_PROJECT_ID``` is the Project ID 
   1. This can be found on https://vercel.com/[your-team-name]/[your-project-name] on the ```Settings``` tab 
8. ```VERCEL TOKEN``` is a token that allow other apps to control your whole account. Be careful! 
   1. Go to ```Account Settigns``` and click on the ```Tokens``` tab 
   2. Choose a specific token name like [project-name-github-actions-workflow] and select the scope as the Team that is working on the project.
   3. Choose an expiration date and click ```Create```. 
   4. Copy the token and store it somewhere temporarily (don't share it with anyone!)
9. Now that you have all the environment variables, go to your GitHub repository and click ```Settings```
10. Go to ```Secrets and variables``` and click ```Actions```
11. Add each repository secret 
    1. Write the environment variable as the Name (e.g. VERCEL_ORG_ID) and paste the secret on Secret. 
12. Push any changes to the repository to run the workflow 

## How to setup for branches (For non-production environments)?
1. Create a ```.github``` folder in the root folder of your application 
    1. This will automatically create a ```github/workflows``` folder 
2. Inside the github/workflows folder, create a ```preview.yaml``` file 
3. Copy the following code snippet and paste it to the ```preview.yaml``` file. 
   1. Please note that it adds ```main``` to ```branches-ignore:```
   2. Configure to your needs and add your ```branch-name``` to a ```branch:``` if you only want it to run on a specific branch. 
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
4. Follow the same steps as above for production environments. 

## FAQ 
1. #### How do I create a .github folder? 
   1. This can be done manually by creating a .github folder and pushing your changes 
   2. Go to the repository on github.com 
   
2. #### Where do I find my VERCEL_ORG_ID? 
3. #### Where do I find my VERCEL_PROJECT_ID?
4. #### Where do I find my VERCEL_TOKEN? 
5. #### Is this allowed? Doesn't Vercel want us to upgrade to the Pro plan to collaborate? 
   1. Yes, this is allowed. In fact, it is mentioned in their own official guide (link below). 

## Context  
Vercel has disabled collaboration on hobby plan. This means that only the repository owner is able to commit and deploy the changes to a Vercel Hobby project. Now users have to be invited to a team and pay U$20/month per user to collaborate. 

I love Vercel and their services (in fact, I use Vercel for everything and have bought many domains using Vercel), but there is no way that I'm paying U$20/month for each user on a project. I find that really crazy. If I have 5 people working on a repository, it'll be U$100/month for one team. Say I have many teams: that might cost thousands of dollars a month. 

## Collaborators 
Collaborators are welcome! Feel free to help me if I'm missing anything or if you have configuration suggestions for a specific workflow! 

## Resources 
1. https://vercel.com/guides/how-can-i-use-github-actions-with-vercel

## Tags (ignore)
- Vercel Hobby Plan Collaboration 
- Vercel Hobby Tier Collaboration 
- Vercel Free Tier Collaboration 
- Vercel Hobby Plan Deployment 
- Vercel Hobby Plan CI/CD 
- Vercel Hobby Plan Automatic Deployment GitHub Actions 
