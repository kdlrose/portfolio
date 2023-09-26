---
layout: post
title: "jekyll &rarr; github pages"
tags: dev, ruby, jekyll
category: dev
---

When Squarespace bought Google Domains, I took one look at Netlify's prices and decided this was an invitation to disappear, by which I mean scrub my government name from the Internet wherever possible and return into early aughts [pseudonymity]({% post_url 2023-09-05-huoxing %}) as I let the Google CNAME contract run out. This involved doing something about my Substack archive. I considered importing it to a service like Buttondown, but I've also developed a revulsion toward the concept of newsletters. (I am mentally ill.)

I had every intention of using a pre-existing Jekyll theme, but none could be served locally. The ruby message that came up said something like

`every version of [THEME GEM] depends on jekyll ~> 3.9 and Gemfile depends on jekyll 4.2 ... version solving has failed`

Rather than taking one second to look at my Gemfile or check my Jekyll version, I gave myself the excruciating assignment of building my own theme. "My own theme" is generous. This is a chimera of [no style, please!](https://github.com/riggraz/no-style-please/), [elly.town](https://elly.town), and [jekyll-theme-console](https://github.com/b2a3e8/jekyll-theme-console). I trace the problems that plagued me to two things:

1. `gem github-pages`
2. using ruby on Windows 10

## gem github-pages

On the Windows desktop I was working with at the time, I guilelessly ran `gem install jekyll` for the latest stable release. This meant that my new theme's generated gemspec said `spec.add_runtime_dependency "jekyll", "~> 4.3.2"`. Little did I know `gem github-pages` depends on 3.9.3, or that I could easily edit the gemspec. I had two versions of jekyll on my machine and stayed baffled that my local server build kept using 4.3.2 for several hours.

The other dependency jekyll 3.9.3 came with was jekyll-sass-converter 1.5.2. sass was the main reason I was confident I could figure out jekyll theming, but by sass I meant `dart sass`, not this old version of jekyll-sass-converter's LibSass, deprecated as of *checks notes* 2020. This is so sick and twisted to me. Why hasn't github-pages caught up? v228 was released February of this year. The imperfect fix for this was adding the newer `sass-embedded` to my Gemfile, but I still have these ancient, unused gems floating around for the sake of jekyll 3.9.3. I haven't written a single line of actual .rb, but this site takes 94 gems as of writing, so I think I get what people mean by ruby being slow and huge.

## ruby on Windows

Windows thwarted me both locally and remotely. Locally, I got

`Warning: the running version of Bundler (2.1.4) is older than the version that created the lockfile (2.4.19).`

Once again, an issue of having multiple versions of a gem installed, though I had no idea where bundler 2.1.4 came from. Any attempt to remove 2.1.4 printed that it was a default gem that could not be uninstalled. I made 2.4.19 default, too, but that didn't help. I went into the /usr/local/bin/ruby.../spec folder to try to get rid of it, to no avail. This prompted me to switch to my MacBook Air, where the local build went off without a hitch.

The next issue was deploying to GitHub Pages from Windows since I wanted it to work on all my devices. I don't enjoy GitHub Actions, but its failed deployment message tipped me off to

`bundle lock --add-platform x86-mingw32 x64-mingw32 x86-mswin32 java`

This added to my Gemfile

```UTF-8
platforms :mingw, :x64_mingw, :mswin, :jruby do
  gem "tzinfo", ">= 1", "< 3"
  gem "tzinfo-data"
end

gem "wdm", "~> 0.1.0", :install_if => Gem.win_platform?
```

and a StackOverflow question directed me to the bundler [documentation](https://bundler.io/v2.3/man/gemfile.5.html#PLATFORMS) on platforms. The irony, of course, is I've quit using that Windows machine in favor of my MacBook Air and brand new Arch Linux ProBook, but I guess it's nice to know my bases are covered.
