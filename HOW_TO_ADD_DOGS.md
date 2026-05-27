# How to add a new training video

The site is one file: `index.html`. The "Recent work" section pulls in any Instagram post you want to feature. Adding, removing, or reordering posts is a 30-second job.

## 1. Get the Instagram post link

Open the post on Instagram and copy the URL from the browser's address bar. It will look like:

    https://www.instagram.com/p/Cxyz123abcd/

or for a reel:

    https://www.instagram.com/reel/Cxyz123abcd/

The post must be **public** on `@manwithawhistle` for the embed to render.

## 2. Open `index.html` and find the post grid

Search for:

    HOW TO ADD A NEW VIDEO

That comment sits just above `<div class="post-grid">`, which contains the post blocks.

## 3. Paste the URL into a post block

Each post on the page is one block like this:

    <article class="post-frame reveal">
      <div class="ig-slot">
        <blockquote class="instagram-media"
          data-instgrm-permalink="https://www.instagram.com/p/PASTE_HERE/"
          data-instgrm-version="14"></blockquote>
      </div>
    </article>

Replace the URL inside `data-instgrm-permalink="..."` with your Instagram post link. Save the file — Instagram's embed script renders the rest automatically when the page loads.

## To add more posts

Copy any existing `<article class="post-frame reveal">…</article>` block, paste it underneath, then change the URL. The grid is three across on desktop and re-flows automatically.

## To remove a post

Delete the entire `<article class="post-frame reveal">…</article>` block.

## To reorder

Just move the blocks up or down in the file — top of the file shows first on the page.

## Tips

- Only **public** Instagram posts will render. Private accounts or hidden posts will appear as a blank Instagram card.
- Reels and feed posts both work — use whichever URL Instagram gives you.
- After editing, the change goes live as soon as you redeploy (drag the new zip onto Netlify, or `git push` if you've set up Git deploys).
