# Local Build

This might not be the most versatile, but it's quite easy and my recommended way of getting the project set up. This project is heavily dependent on [pnpm](https://pnpm.io) and it's monorepo workspaces feature, so I'd pick that up if I were you.

1. Once you have `pnpm` set up, you can pull the source code from GitHub and dive into the repository.

```bash
git clone git@github.com:lazy-media/Reactive-Resume.git
cd Reactive-Resume
```

1. Install dependencies using [pnpm](https://pnpm.io/), and it should take just a few minutes.

```bash
pnpm install
```

1. Copy the `.env.example` file to `.env` in the project root and fill it with values according to your setup. To know which environment variables are required, and about what they do, head over [this section](https://docs.rxresume.org/source-code/environment-variables).

```bash
cp .env.example .env
```

1. Run the app locally by using the command:

```bash
pnpm dev
```

Now, your **frontend** client should be running on [`http://localhost:3000`](http://localhost:3000), your **backend** server on [`http://localhost:3100`](http://localhost:3100) and this **documentation** on [`http://localhost:3200`](http://localhost:3200).

1. To ensure that the app works currently, a proxy layer has to be made between the client and server. For this, I made use of a Chrome extension called [**Rabbit URL Rewriter**](https://chrome.google.com/webstore/detail/rabbit-url-rewriter/kcbmcmeblpkcndhfhkclggekfblookii?hl=en) to forward my requests made to `localhost:3000/api` to `localhost:3100`. The configuration should look something like this:

```
Website URL: http://localhost:3000
From URL: http://localhost:3000/api/(.*)
To URL: http://localhost:3100/$1
```

![Screenshot 2022-03-12 at 4 07 37 PM](https://user-images.githubusercontent.com/1134738/158023473-d415e696-f027-4bc7-af02-648c4a99b147.png)

Now, you should be able to create accounts, login etc.

1. Build the project before deploying by running the command:

```bash
pnpm build
```

1. Finally, start the production servers for all three workspaces by running:

```bash
pnpm start
```

Additionally, you can check the `package.json` for some additional scripts on how to run commands for a specific workspace. For more information on pnpm workspaces, head over to [their documentation](https://pnpm.io/workspaces).
