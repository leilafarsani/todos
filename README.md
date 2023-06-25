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
- npx prisma migrate dev --name init (Now I get my Your database is now in sync with your schema.)
- build use the database inside the project, so inside the src folder I created a file and called it db.ts and I write some codes in it

##### Note. 
The reason I did this in the new file is that the way that Next.js works in development mode, its likes to do something called hot reloading which is essentially only will send out the files that need to be change and its kind of reruns things without actually restarting our entire server so makes things faster for us but it has a bit of problem with prisma because what happens is it tries to constantly create new connection to our PrismaClient, so if we go to Prisma documentation, there is actually an article to solve this problem so we use that code and put it in our db.ts file
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