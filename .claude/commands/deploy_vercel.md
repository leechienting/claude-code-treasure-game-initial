Deploy the current project to Vercel and return the live URL.

## Steps

1. Check if the Vercel CLI is installed by running `vercel --version`. If not installed, run `npm install -g vercel` first.

2. Check if the user is logged in to Vercel by running `vercel whoami`. If not logged in, instruct the user to run `! vercel login` in the prompt so the interactive login lands in the conversation.

3. Run `npm run build` to ensure the project builds successfully before deploying. If the build fails, report the errors and stop.

4. Deploy to Vercel by running:
   ```
   vercel --yes --prod
   ```
   The `--yes` flag accepts all default prompts automatically. The `--prod` flag deploys to the production URL.

5. Parse the output to find the production deployment URL (the line starting with `Production:` or the final URL ending in `.vercel.app`).

6. Report the live URL to the user so they can open it in a browser.
