# Group 1 Local Homepage Work Log

**Contributor:** Thando  
**Project:** Zonke Marketplace  
**Work type:** Local development and homepage interface prototype  
**Status:** Implemented in the local project folder. This document and code have not yet been pushed or merged to GitHub.

## What I worked on

I set up the existing Zonke project on my computer, opened the local homepage, and made a small interface prototype based on the reference image I supplied. My visible changes are:

1. Updated the homepage search hint so it mentions both jobs and talent.
2. Changed the homepage banner to a dark teal gradient inspired by my reference (`#224444` is one of the middle gradient colors).
3. Added a pill-shaped homepage navigation bar with Home, Client, Freelance, Search, and an account icon.
4. Connected the navigation items to routes that already exist in the Laravel project.

The navigation and colors are a prototype. I previewed the color and search text in the local app. The navigation was added after the last screenshot, so it still needs a fresh visual check and screenshot before I submit the evidence.

## Step 1 — Local environment setup

I used XAMPP for Apache and MariaDB/MySQL. My XAMPP installation reported PHP 8.2.12, but the project required PHP 8.3 or later for its Composer dependencies. I downloaded and extracted the official PHP 8.3.35 Windows ZIP build separately; this did not replace XAMPP's PHP.

The extracted PHP folder used in my Command Prompt was:

```text
C:\Users\thand\Downloads\php-8.3.35-nts-Win32-vs16-x64
```

I copied `php.ini-development` to `php.ini`, set the extension directory, and enabled the extensions needed by the project/database connection:

```ini
extension_dir = "ext"
extension=curl
extension=fileinfo
extension=gd
extension=intl
extension=mbstring
extension=mysqli
extension=openssl
extension=pdo_mysql
extension=zip
```

I confirmed the extracted CLI version with:

```bat
"C:\Users\thand\Downloads\php-8.3.35-nts-Win32-vs16-x64\php.exe" -v
```

It reported PHP 8.3.35. I also checked that the listed extensions appeared in the CLI module list.

## Step 2 — Database and local Laravel app

I started Apache and MySQL from the XAMPP Control Panel, opened phpMyAdmin, and imported the provided database into a local database named `zonke_local`. phpMyAdmin showed that the import completed successfully.

In the local project folder, I created `.env` from `.env.example` and set the local database name to `zonke_local`. I generated the Laravel application key and started the development server with PHP 8.3:

```bat
cd /d "C:\Users\thand\Documents\Codex\2026-10-16\referenced-chatgpt-conversation-this-is-an\work\Zonke-Revamp-Team"
"C:\Users\thand\Downloads\php-8.3.35-nts-Win32-vs16-x64\php.exe" artisan key:generate
"C:\Users\thand\Downloads\php-8.3.35-nts-Win32-vs16-x64\php.exe" artisan serve
```

The local preview address is:

```text
http://127.0.0.1:8000
```

Keep the Command Prompt running `artisan serve` open while viewing the local site.

## Step 3 — Search placeholder change

**File:** `resources/views/templates/basic/partials/banner.blade.php`

I changed the homepage search field from a jobs-only hint to wording that also includes talent. The final input element is:

```blade
<input type="search" name="search" class="form--control" placeholder="@lang('Search for jobs or talent')">
```

This better matches the search dropdown, which already lets visitors choose Job or Talent.

## Step 4 — Dark teal homepage banner

**Files:**

- `public/assets/templates/basic/css/main.css` — the `.banner-section` background declaration.
- `resources/views/templates/basic/partials/banner.blade.php` — the homepage section's inline background, used so the local preview immediately reflects the selected color.

The gradient uses the requested teal `#224444` in the middle, with darker teal tones at the top and bottom:

```css
background: linear-gradient(180deg, #142225 0%, #192E31 21%, #224444 58%, #1C3839 86%, #142225 100%);
```

The banner opening tag in the Blade view is:

```blade
<section class="banner-section" style="background: linear-gradient(180deg, #142225 0%, #192E31 21%, #224444 58%, #1C3839 86%, #142225 100%) !important;">
```

The CSS declaration is in the `.banner-section` rule in `main.css`. A temporary edit was initially applied to the wrong footer selector during the color change; I restored the footer rule and placed the final gradient on the banner.

## Step 5 — Homepage navigation links

**File:** `resources/views/templates/basic/partials/banner.blade.php`

I placed the navigation inside the homepage banner, above the banner content:

```blade
<div class="home-nav container">
    <nav class="home-nav__links" aria-label="Main navigation">
        <a class="home-nav__link is-active" href="{{ route('home') }}">Home</a>
        <a class="home-nav__link" href="{{ auth()->guard('buyer')->check() ? route('buyer.home') : route('buyer.register') }}">Client</a>
        <a class="home-nav__link" href="{{ auth()->check() && !auth()->guard('buyer')->check() ? route('user.home') : route('user.register') }}">Freelance</a>
        <a class="home-nav__link" href="{{ route('freelance.jobs') }}">Search</a>
    </nav>
    <a class="home-nav__account" href="{{ auth()->guard('buyer')->check() ? route('buyer.home') : (auth()->check() ? route('user.home') : route('buyer.login')) }}" aria-label="Account">
        <i class="las la-user" aria-hidden="true"></i>
    </a>
</div>
```

The links use existing Laravel route names:

| Button | Destination |
| --- | --- |
| Home | `home` — homepage |
| Client | `buyer.register`, or `buyer.home` for a signed-in buyer |
| Freelance | `user.register`, or `user.home` for a signed-in freelancer |
| Search | `freelance.jobs` — job listings/search |
| Account icon | `buyer.login` for a guest, or the signed-in account dashboard |

## Step 6 — Homepage-only header behavior

**File:** `resources/views/templates/basic/partials/header.blade.php`

The project already had a separate full header. I wrapped that existing header so it remains visible on other pages but is hidden on the homepage, where the new reference-style bar is shown instead:

```blade
@if (!request()->routeIs('home'))
    {{-- Existing site header markup remains here. --}}
@endif
```

The `@if` is immediately after the PHP setup block at the top of the file, before `<header class="header" id="header">`. The closing `@endif` is after the existing header's closing `</header>`.

## Step 7 — Navigation styling

**File:** `public/assets/templates/basic/css/custom.css`

I added the following CSS at the end of the custom stylesheet. It centers the rounded pill, gives the active Home item a black background, puts the account icon on the right, and reduces spacing on small screens:

```css
.home-nav {
  position: absolute;
  top: 22px;
  left: 0;
  right: 0;
  z-index: 5;
  display: flex;
  justify-content: center;
  align-items: center;
  pointer-events: none;
}

.home-nav__links {
  display: flex;
  align-items: center;
  gap: 2px;
  padding: 5px;
  border: 1px solid rgba(0, 0, 0, 0.48);
  border-radius: 999px;
  background: rgba(7, 30, 31, 0.56);
  box-shadow: inset 0 1px 8px rgba(0, 0, 0, 0.35);
  pointer-events: auto;
}

.home-nav__link {
  display: inline-flex;
  min-height: 42px;
  align-items: center;
  justify-content: center;
  padding: 0 18px;
  border-radius: 999px;
  color: #f5f7f6;
  font-size: 15px;
  font-weight: 600;
  text-decoration: none;
  text-transform: lowercase;
  transition: background-color 0.2s ease, color 0.2s ease;
}

.home-nav__link:hover,
.home-nav__link:focus-visible,
.home-nav__link.is-active {
  background: #050a0b;
  color: #fff;
}

.home-nav__account {
  position: absolute;
  right: 15px;
  display: grid;
  width: 44px;
  height: 44px;
  place-items: center;
  border-radius: 50%;
  background: #050a0b;
  color: #fff;
  font-size: 25px;
  text-decoration: none;
  pointer-events: auto;
}

.home-nav__account:hover,
.home-nav__account:focus-visible {
  color: #fff;
  background: #132b2c;
}

@media (max-width: 575px) {
  .home-nav {
    top: 16px;
  }

  .home-nav__links {
    margin-right: 47px;
    padding: 4px;
  }

  .home-nav__link {
    min-height: 38px;
    padding: 0 10px;
    font-size: 13px;
  }

  .home-nav__account {
    right: 0;
    width: 40px;
    height: 40px;
  }
}
```

## Evidence to add

Before uploading this document to GitHub, add your own screenshots in an `evidence` folder and link them here. The two reference images show the intended design; use a fresh screenshot of the finished local page as the result evidence.

- `evidence/homepage-navigation-result.png` — local homepage after refresh, showing the new bar and teal banner.
- `evidence/banner-code.png` — VS Code showing the banner markup and updated search placeholder.
- `evidence/navigation-code.png` — VS Code showing the navigation markup/CSS.
- `evidence/local-server.png` — optional screenshot showing the local server running at `127.0.0.1:8000`.

## AI assistance acknowledgment

I used ChatGPT as an AI assistant while completing this work. I shared my design reference, followed the local setup steps, chose the color and navigation labels, and checked the color/search result in my local browser. ChatGPT helped me locate the relevant files, explain the setup commands, and draft/apply the code changes. I am documenting both my choices and the AI assistance clearly. Before I submit, I will refresh the homepage, check the new navigation links, and capture a screenshot of the final result.

## GitHub sharing note

My intended destination is [my WIL repository](https://github.com/thandotkz72-source/WIL). This project folder is currently an extracted local copy and is not connected to GitHub. I have not pushed or merged these changes. When transferring the work to a GitHub repository, upload only the intended source files, this report, and the evidence screenshots. Do not upload `.env`, the local SQL dump, or the `vendor` folder. If I contribute to the shared Zonke team repository, I should use a branch and pull request.
