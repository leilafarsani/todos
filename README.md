# todos
### Technologies 
- Next.js
- TapeScript
- Prisma
- Sqlite

### Setting Dev Environment 
npx create-next-app@latest todo-next

✔ Would you like to use TypeScript with this project? Yes
✔ Would you like to use ESLint with this project? Yes
✔ Would you like to use Tailwind CSS with this project? Yes
✔ Would you like to use `src/` directory with this project? Yes
✔ Use App Router (recommended)? Yes
✔ Would you like to customize the default import alias? Yes
✔ What import alias would you like configured? … @/*

#### Getting set up with Prisma

- npm i prisma --save-dev
- npx prisma init --datasource-provider sqlite
- Creating Todo model in the schema.prisma
- Update .env file based on my Sqlite db which names mydatabase.db to
   DATABASE_URL="file:~/mydatabase.db
- npx prisma migrate dev --name init (Now my  database is now in sync with schema.prisma)
- build use the database inside the project, so inside the src folder I created a file and called it db.ts and I wrote some codes in it.

##### Note. 
The reason I did this new file is that, the way that Next.js works in development mode, its likes to do something called hot reloading which is essentially only will send out the files that need to be change and its kind of reruns things without actually restarting our entire server so makes things faster for us but it has a bit of problem with prisma because what happens is, it tries to constantly create new connection to our PrismaClient, so if we go to Prisma documentation, there is actually an article to solve this problem so we use that code and put it in our db.ts file
````
import { PrismaClient } from "@prisma/client"

const globalForPrisma = global as unknown as {
  prisma: PrismaClient | undefined
}

export const prisma =
  globalForPrisma.prisma ??
  new PrismaClient({
    log: ["query"],
  })

if (process.env.NODE_ENV !== "production") globalForPrisma.prisma = prisma
````
In the code above we are just importing our Client on the top and all what we are doing is that we are taking a global object and we are just adding in our PrismaClient to this. Also, we are creating a variable called prisma and we either setting it to that variable that we already created that global variable or we are creating a brand new PrismaClient. And finally down there we are saying that if we are not in production then get prisma from that global variable. So essentially what it does is it instantiates a singleton. So, essentially no matter how many times I try to access this prisma variable it's only ever going to create one single client which is great for when we have that hot module reloading inside of development and it just prevents us from having tons of clients being spun up when we don't actually want that.

#### Note: 
I put .env and also mydatabase.db* for all related mydatabbase.db files in the .gitignore file. 

### Starting my Application 

- npm run dev (start my app in dev environment)

- In globals.css I delete everything but tailwind stuff as that's all I care about.
- In page.tsx I deleted everything and for now just export Home function like 
`````
export default function Home(){
  return <h1>My Todos</h1>
}
`````
- In my layout page I changed a few things like title and className for body and etc. I am gonna keep inter className which is essentially a font I can get from Next. Next has all the google font built-in.
#### Routing 
- In Next13 the way that everything works is inside of our application. The way routing works is based on folders. Inside each folders there should be a page.tsx and we can almost think of that as like an index file essentially. So I wanted to add a new route to like /new page, so I created a new folder called new and inside that I created a new file called page.tsx and that would be how I routed this file. Essentially everything I put in this page.tsx file is going to be what routes to the /new route. Let's say I will need another page. So I created another folder and called it newer and inside of it I created a page.tsx file to write my code in it. For now I just created a simple function to return a simple text.
#### Note
We can access to these new pages by slash and name of the folder we want(which is the same as the name of the page we need to access), wether at the end of URL or as an href for a Link tag.

#### Link
Link has a built-in Link component. It works just like an a tag.And all that routing stuff happens on the client so if I click on it, it doesn't actually do a full page refresh or it doesn't do like all of that. All it does  all the client routing just like we are used to  in a normal react application. I just need to make sure that I imported Link by 
`````
import Link from "next/link";
`````
#### List my todos

I am going to loop through all of the todos and I want each one of the todos render out something. But how do I get these todos? Normally inside of the react application I would use something like useQuery or I would make a fetch request inside of a useEffect and I would get my to-dos that way but with SERVER COMPONENTS inside of Next.js 13 we don't need to do any of them. As long as I am using the app router which I am already configured when I was actually did my initialization. As we can see inside this app folder as long as I have that, I have the ability to call server code inside of our components and its going to run all this on the server and then just send down the data to the client. What is really great about this is that I can turn Home function into an async function and right there I can make a call to anywhere that I want, for example I can call our database by saying prisma.todos.findMany()
(I need to make sure that I imported the prisma (the variable I made in db.ts)) and define a variable called todos for that. So, I am able to loop through that variable and do whatever I want to that, for example now I just want to list them.  (Their titles)

When I did that I noticed something interesting, in the log I got a msg that it said I queried my database but I don't see change in my app as there are no todos in our dbb yet. So I made a query to my database from my server because all the logs in the terminal are from our server but I wrote my code inside a react component. This is a really cool thing about react server components. As long as my code doesn't do anything on the client like we don't have any useEffects, useStates, or onChange event listeners, it's going to run all of your codes on the server then it's going to send that down to the client which means I can do things like call my database from this component and none of that actual server database code is going to get to the client so all your code is still secure on the server but all the data from that like for example our todo array here, is going to make its way down to the actual client which is really great. Now to see what this looks like I am actually going to fake adding a todo to my database and we should see test in the page when refreshing.
Also, if I view the source (click line wrap) the source I can see what is happening we can find our todos, ul and li with a text of test, which means what happened my server generated all the HTML and it sent it down to the client so it didn't have to to do anything on the client. The client got essentially raw HTML and it's all it has to deal with, which is really great so we don't need to worry about loading states data fetching and so on. It's all handled for us with Next.js .

##### Note 

I am just gonna break my code to different functions so they would be more reusable and also try to make my code nicer and cleaner.
I make a component called TodoI
