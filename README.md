### Prisma multiple clients in Yarn 1 Workspace reproduction

Steps to reproduce:
1. Check that theres no prisma folder in api/node_modules
```
ls -la api/node_modules
```
2. Go into api and generate prisma client
```
cd api && yarn rw prisma generate
```
3. Notice that prisma has now been generated in api node_modules
```
❯ ls -a api/node_modules
.       ..      .prisma
```

And to see the actual problem this causes run:

```
yarn rw prisma migrate dev
# equivalent to
# yarn prisma migrate dev --schema "./api/db/schema.prisma"
```

and then
```
yarn rw prisma db seed
```


You should see an error like this:
```
PrismaClientKnownRequestError:
Invalid `db.post.create()` invocation in
/Users/dac09/Experiments/prisma-repro/scripts/seed.ts:24:41

  21
  22 Promise.all(
  23   posts.map(async (post) => {
→ 24     const newPost = await db.post.create(
  The table `main.Post` does not exist in the current database.
    at Object.request (/Users/dac09/Experiments/prisma-repro/node_modules/@prisma/client/runtime/index.js:39809:15)
    at PrismaClient._request (/Users/dac09/Experiments/prisma-repro/node_modules/@prisma/client/runtime/index.js:40637:18)
    at /Users/dac09/Experiments/prisma-repro/scripts/seed.ts:24:27
    at async Promise.all (index 0) {
  code: 'P2021',
  clientVersion: '3.10.0',
  meta: { table: 'main.Post' }
  ```
