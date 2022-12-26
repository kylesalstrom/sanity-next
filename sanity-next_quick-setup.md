# [Tutorial: Make a blog with Next.js, React, and Sanity](https://www.sanity.io/blog/build-your-own-blog-with-sanity-and-next-js)

___
## Create a project folder and a monorepo
```
~/Sites/project-name
  ├── studio
  └── web
```
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

### Fire it up
npm run dev

___
## All Together

### Connect Sanity App
- in web app run `npm install groq` to install projections syntax highlighting
- `npm install @sanity/client` to install necessary dependencies for connecting to the Sanity API
- create /web/client.json and add the following:
```
import sanityClient from '@sanity/client'

export default sanityClient({
  projectId: 'your-project-id', // you can find this in /studio/sanity.config.js
  dataset: 'production', // or the name you chose in step 1
  useCdn: true // `false` if you want to ensure fresh data
})
```

### Make a dynamic page template in next app
- Add a `post` directory and `[slug].js` file to your pages folder as well, it should now look like this:
```
~/Sites/my-blog/web
  ├── client.js
  ├── package-lock.json
  ├── package.json
  └── pages
    ├── index.js
    └── post
      └── [slug].js
```
- Set up a demo page with Sanity projections
```
// [slug].js

import client from '../../client'

const Post = (props) => {
  const { title = 'Missing title', name = 'Missing name' } = props.post
  return (
    <article>
      <h1>{title}</h1>
      <span>By {name}</span>
    </article>
  )
}

export async function getStaticPaths() {
  const paths = await client.fetch(
    `*[_type == "post" && defined(slug.current)][].slug.current`
  )

  return {
    paths: paths.map((slug) => ({params: {slug}})),
    fallback: true,
  }
}

export async function getStaticProps(context) {
  // It's important to default the slug so that it doesn't return "undefined"
  const { slug = "" } = context.params
  const post = await client.fetch(`
    *[_type == "post" && slug.current == $slug][0]{title, "name": author->name}
  `, { slug })
  return {
    props: {
      post
    }
  }
}

export default Post
```
[Read More About Next.js Dynamic Routes Here](https://nextjs.org/docs/routing/dynamic-routes)
[Read More About Sanity's Slug URL Creation Here](https://www.sanity.io/blog/build-your-own-blog-with-sanity-and-next-js#2dac7329b33e)

### Get Fancier
- in web app run `npm install @sanity/image-url` to install the sanity image url-package to make fetching image and file 
assets easier. Usage:
```
import imageUrlBuilder from '@sanity/image-url'
```
```
function urlFor (source) {
  return imageUrlBuilder(client).image(source)
}
```
```
return (
  <img
    src={urlFor(props.authorImage)
      .width(200)
      .url()}
  />
)
```
- in web app run `npm install @portabletext/react` to install support for portable text content. Usage:
```
import {PortableText} from '@portabletext/react'
```
```
const ptComponents = {
  types: {
    image: ({ value }) => {
      if (!value?.asset?._ref) {
        return null
      }
      return (
        <img
          alt={value.alt || ' '}
          loading="lazy"
          src={urlFor(value).width(320).height(240).fit('max').auto('format')}
        />
      )
    }
  }
}
```
```
return (
  <PortableText
    value={props.body}
    components={ptComponents}
  />
)
```