# Host Your Resume on AWS EC2 with a CI/CD Setup Using GitHub Actions

This guide will help you set up an automated deployment pipeline for your resume on an AWS EC2 instance using GitHub Actions.

## Steps to Follow

### Step 1: Create an EC2 Instance and Download the Key Pair
- Launch an EC2 instance on AWS.
- Download the private key (`.pem` file) for SSH access.

### Step 2: Create Secrets in GitHub for the Repository
Add the following secrets in your GitHub repository:
- `EC2_SSH_KEY`: The private key (`.pem`) used to log in to the instance.
- `HOST_DNS`: The public DNS of the instance (e.g., `ec2-xx-xxx-xxx-xxx.us-west-2.compute.amazonaws.com`).
- `USERNAME`: The username for SSH access (e.g., `ubuntu`).
- `TARGET_DIR`: The directory where the resume files will be deployed.

### Step 3: Creating Your First Workflow

1. Create a `.github/workflows` directory in your GitHub repository if it does not exist.
2. Inside this directory, create a file named `github-actions-ec2.yml`.
3. Define the jobs in this file.

#### Sample `github-actions-ec2.yml`:

```yaml
name: Push-to-EC2

# Trigger deployment only on push to main branch
on:
  push:
    branches:
      - main

jobs:
  deploy:
    name: Deploy to EC2 on main branch push
    runs-on: ubuntu-latest

    steps:
      - name: Checkout the files
        uses: actions/checkout@v2

      - name: Deploy to Server 1
        uses: easingthemes/ssh-deploy@main
        env:
          SSH_PRIVATE_KEY: ${{ secrets.EC2_SSH_KEY }}
          REMOTE_HOST: ${{ secrets.HOST_DNS }}
          REMOTE_USER: ${{ secrets.USERNAME }}
          TARGET: ${{ secrets.TARGET_DIR }}

      - name: Executing remote SSH commands using SSH key
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.HOST_DNS }}
          username: ${{ secrets.USERNAME }}
          key: ${{ secrets.EC2_SSH_KEY }}
          script: |
            sudo apt-get -y update
            sudo apt-get install -y apache2
            sudo systemctl start apache2
            sudo systemctl enable apache2
            cd home
            sudo mv * /var/www/html
```
![image](https://github.com/user-attachments/assets/a7423457-e654-4735-aff3-9156ccb2c006)

### Step 4: Testing
- Push changes to the `main` branch and verify if GitHub Actions automatically deploys the code to the EC2 instance.
- You can monitor the workflow execution in the "Actions" tab of your GitHub repository.

## Conclusion
By following these steps, you have successfully set up a CI/CD pipeline to deploy your resume to AWS EC2 automatically using GitHub Actions. 🎉
