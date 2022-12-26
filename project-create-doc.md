# [Tutorial: Make a blog with Next.js, React, and Sanity](https://www.sanity.io/blog/build-your-own-blog-with-sanity-and-next-js)

___
## Create a project folder and a monorepo
~/Sites/project-name
        ├── studio
        └── web

___
## Studio: Create Sanity Studio React App 
### Install Sanity and the preconfigured blog schemas
- `npm i -g @sanity/cli` install the Sanity Command Line (CLI) tooling
- `npm create sanity@latest` launch the Studio

___
## Web: Create Next.js React App 
### Install Next.js and get it running
- `npm init -y` create a package.json
- `npm install next react react-dom` install next.js dependencies
- add the following to your package.json
```
{
  "scripts": {
    "dev": "next",
    "build": "next build",
    "start": "next start"
  }
}
```
- package.json should look similar to this:
```
{
  "name": "web",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "dev": "next",
    "build": "next build",
    "start": "next start"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "next": "^12.0.3",
    "react": "~17.0.2",
    "react-dom": "^17.0.2"
  }
}
```
- create test page at pages/index.js
```
// index.js
const Index = () => {
    return (
      <div>
        <p>Hello world!</p>
      </div>
    )
}

export default Index;
```

### fire it up
npm run dev

___
## All Together
### Make a dynamic page template in next app

- in web app run `npm install @sanity/client` to install necessary dependencies for connecting to the Sanity API