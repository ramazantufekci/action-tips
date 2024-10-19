## Action Tip 1

```yaml
on: push
jobs: 
  first-job:
    runs-on: windows-latest
    steps:
      - run: node --version
      - run: npm --version
```


## Action Tip 2

container çalıştır versiyonunu değiştirerek istediğin node versiyonunu kullan

```yaml
on: push
jobs: 
  first-job:
    runs-on: ubuntu-latest
    container: node:17.6.0
    steps:
      - run: node --version
      - run: npm --version
```

## Action Tip 3

nodejs örneği 

```yaml
name: action tips
on: push
jobs: 
  build-node:
    runs-on: ubuntu-latest
    container: node:16
    steps:
      - run: node --version
      - run: npm --version
      - uses: actions/checkout@v3
      - run: npm install moment
      - run: node app.js
```

## Action Tip 4

Yaptığın işlemlere isim ver

```yaml
name: Node Continuous Integration
on: push
jobs: 
  build-node:
    name: Build and Run Node Project
    runs-on: ubuntu-latest
    container: node:16
    steps:
      - run: node --version
        name: Check Node version
      - run: npm --version
        name: Check Npm version
      - uses: actions/checkout@v3
        name: Checkout code from Github
      - run: npm install moment
        name: Install NPM packages
      - run: node app.js
        name: Run Node application
```

## Action Tip 5

nextjs branch inda next.js projesini build etme örneği

```yaml
name: Build Next.js web application
on: 
  push:
    branches:
      - nextjs
jobs: 
  build-node:
    name: Build Project
    runs-on: ubuntu-latest
    container: node:16
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      - name: Install NPM dependencies
        run: npm install
      - name: Build project assets
        run: npm run build
```

## Action Tip 6

nextjs branch ında upload artifact örneği  include-hidden-files bu propu true yapmadan dosyayı bulamıyorum hatası verdi.

```yaml
name: Build Next.js web application
on: 
  push:
    branches:
      - nextjs
jobs: 
  build-project:
    name: Build Project
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      - name: Install NPM dependencies
        run: npm install
      - name: Build project assets
        run: npm run build
      - name: yeri göster
        run: ls -al .next/
      - name: Upload static site content
        uses: actions/upload-artifact@v4
        with:
         name: static-site
         path: .next/
         include-hidden-files: true
```

## Action Tip 7

nextjs branch ında download artifact örneği

```yaml
name: Build Next.js web application
on: 
  push:
    branches:
      - nextjs
jobs: 
  build-project:
    name: Build Project
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      - name: Install NPM dependencies
        run: npm install
      - name: Build project assets
        run: npm run build
      - name: yeri göster
        run: ls -al .next/
      - name: Upload static site content
        uses: actions/upload-artifact@v4
        with:
         name: static-site
         path: .next/
         include-hidden-files: true
  release-project:
    name: Release project
    runs-on: ubuntu-latest
    needs: build-project
    steps:
      - name: Download artifact
        uses: actions/download-artifact@v4
        with:
          name: static-site
      - name: Test artifact download
        run: ls -R
```

## Action Tip 8

nextjs branch ında zip örneği

```yaml
name: Build Next.js web application
on: 
  push:
    branches:
      - nextjs
jobs: 
  build-project:
    name: Build Project
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      - name: Install NPM dependencies
        run: npm install
      - name: Build project assets
        run: npm run build
      - name: yeri göster
        run: ls -al .next/
      - name: Upload static site content
        uses: actions/upload-artifact@v4
        with:
         name: static-site
         path: .next/
         include-hidden-files: true
  release-project:
    name: Release project
    runs-on: ubuntu-latest
    needs: build-project
    steps:
      - name: Download artifact
        uses: actions/download-artifact@v4
        with:
          name: static-site
      - name: Test artifact download
        run: ls -R
      - name: Archive site content
        uses: thedoctor0/zip-release@master
        with:
          filename: site.zip
```

## Action Tip 9

release oluşturur ve sitenin çalışan halini zip olarak ekler.

```yaml
name: Build Next.js web application
on: 
  push:
    branches:
      - main
jobs: 
  build-project:
    name: Build Project
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      - name: Install NPM dependencies
        run: npm install
      - name: Build project assets
        run: npm run build
      - name: yeri göster
        run: ls -al .next/
      - name: Upload static site content
        uses: actions/upload-artifact@v4
        with:
         name: static-site
         path: .next/
         include-hidden-files: true
  release-project:
    name: Release project
    runs-on: ubuntu-latest
    needs: build-project
    steps:
      - name: Download artifact
        uses: actions/download-artifact@v4
        with:
          name: static-site
      - name: Test artifact download
        run: ls -R
      - name: Archive site content
        uses: thedoctor0/zip-release@master
        with:
          filename: site.zip
      - name: Create Github release
        id: create-new-release
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: ${{ github.run_number }}
          release_name: Release ${{ github.run_number }}
      - name: Upload release asset
        uses: actions/upload-release-asset@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          upload_url: ${{ steps.create-new-release.outputs.upload_url }}
          asset_path: ./site.zip
          asset_name: site-v${{ github.run_number }}.zip
          asset_content_type: application/zip
          

 ```     

## Action Tip 10

docker login işlemi:
bunun için docker accontumuzdan bir token oluşturuyoruz read,write,delete daha sonra repository settings den secrets and variable -> actions -> repository secret altına kullanıcı ismi ve token ın tanımlamasını yapıyoruz.

 ```yaml
on: push
jobs:
  build-container:
    name: Build container
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
    - name: Login to Docker Hub
      uses: docker/login-action@v1
      with:
        username: ${{ secrets.DOCKER_HUB_USERNAME }}
        password: ${{ secrets.DOCKER_HUB_ACCESS_TOKEN }}
 ```

## Action Tip 11

Docker image olarak docker hub push etme örneği

      
 ```yaml
on: push
jobs:
  build-container:
    name: Build container
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
    - name: Login to Docker Hub
      uses: docker/login-action@v1
      with:
        username: ${{ secrets.DOCKER_HUB_USERNAME }}
        password: ${{ secrets.DOCKER_HUB_ACCESS_TOKEN }}
    - name: Build and push to Docker Hub
      uses: docker/build-push-action@v2
      with:
        context: .
        push: true
        tags: ramazantufekci/nextwebapp:latest, ramazantufekci/nextwebapp:${{ github.run_number }}
 ```
