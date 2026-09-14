# Getting Started With Hugo and Deploying to Netlify :zap:
Starter files for Hugo101 article. Read [here](https://bolajiayodeji.com/getting-started-with-hugo-and-deploying-to-netlify-cjyaj1be3000hvjs1z2ipvmuw)

![](https://cdn-images-1.medium.com/max/800/1*oIEz4ooK0G4XUVViipHHjA.png)

## Installation

Use [Git](https://git-scm.com) to clone this Repository into your server folder.

```git
https://github.com/BolajiAyodeji/hugo101.git
```
```git
cd hugo101
```
```
hugo server -D
```
View site on 

```shell
localhost:1313
```

## Google Analytics 4

1. In [Google Analytics](https://analytics.google.com/), create a GA4 property
   (or use your existing GA4 property), then add a **Web** data stream for the
   blog's public URL. Enable **Enhanced measurement** for page views and
   interactions such as scrolling and outbound link clicks.
2. Copy the stream's **Measurement ID** (`G-XXXXXXXXXX`). In `config.toml`, set
   `googleAnalyticsID` under `[Params]` to that ID. Leave it blank to disable
   Analytics. This public identifier is included in the generated HTML.
3. Build with `hugo --gc --minify` and deploy through the usual production
   workflow. The shared layout adds the Google tag to every HTML page;
   page views are sent automatically, so no separate page-view event is needed.
4. Visit the deployed blog and open Analytics **Reports > Realtime** to check
   collection. Initial collection can take up to 30 minutes. An ad blocker may
   block your test visit.

Tracking is excluded from `hugo server`, non-production Hugo environments, and
Netlify contexts other than `production` (including deploy previews and branch
deploys). A normal local `hugo` build uses the production environment and includes
the tag when an ID is configured; use `hugo --environment development` to generate
an untracked local build.

The GA4 partial is in `layouts/partials/google-analytics.html`, called by the
vendored Mainroad base layout. It replaces the retired Universal Analytics tag
and works with the Hugo 0.74.1 version pinned in `netlify.toml`. No Hugo upgrade
or Google Tag Manager container is required.

References: [Google's setup guide](https://support.google.com/analytics/answer/9304153)
and [automatic page views](https://developers.google.com/analytics/devguides/collection/ga4/views).
