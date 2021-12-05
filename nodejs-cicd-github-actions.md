## Deploy Node.js to VPS using Github Actions
> Steps to deploy Node.js to VPS using PM2 and Github Actions


#### 1. Clone repo to vps folder

```jsx
git clone https://github.com/danielwetan/node1.git
```

#### 2. Setup CI workflows

```jsx
# This workflow will do a clean install of node dependencies, build the source code and run tests across different versions of node
# For more information see: https://help.github.com/actions/language-and-framework-guides/using-nodejs-with-github-actions

name: Node.js CI

on:
  pull_request:
    branches: [ master ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Use Node.js 12
      uses: actions/setup-node@v2
      with:
        node-version: 12.x
    - run: npm i
    - run: npm run build --if-present
    - run: npm test
```

#### 3. Setup CD workflows

```jsx
# This is a basic workflow to help you get started with Actions

name: Node.js CD

# Controls when the action will run. 
on:
  # Triggers the workflow on push or pull request events but only for the master branch
  push:
    branches: [ master ]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
    - name: Deploy using ssh
      uses: appleboy/ssh-action@master
      with:
        host: ${{ secrets.HOST }}
        username: ${{ secrets.USERNAME }}
        key: ${{ secrets.PASSWORD }}
        port: 22
        script: |
          cd ~/home/danielwetan/apps/node1
          git pull origin master
          git status
          npm install --only=prod
          pm2 restart node1
```

#### 4. Setup Private Key and Public Key

```jsx
ssh-keygen -t rsa -b 4096 -m PEM -C "github-actions-node1"

// view the key value
cat id_rsa-github-node1.pub
cat id_rsa-github-node1
```

#### 5. Copy public key to `authorized key`

```jsx
cat id_rsa-github-node1

// then copy to

vi ~/.ssh/authorized_keys
```

#### 6. Copy private key to github secret

#### 7. Setup github secret keys

```jsx
Settings -> Secrets -> New secret

PRIVATE_KEY = # Copy generated private key from vps to github secret
HOST = ""
USERNAME = ""
```

Reference:

[https://www.youtube.com/watch?v=pvPJARjqAa8](https://www.youtube.com/watch?v=pvPJARjqAa8)

[https://github.com/appleboy/ssh-action](https://github.com/appleboy/ssh-action)