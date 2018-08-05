# Flip's Website

This is Flip Tanedo's website. Currently hosted on [Netlify](http://fliptanedo.netlify.com).

Live: [http://physics.ucr.edu/~flip/](http://physics.ucr.edu/~flip/).

# Flip's Set Up Notes

These are notes on how I modified the May 2018 version of `Hugo Theme Academic` (George Cushen) to create my personal website. 

## Main Goals

* Update the `Academic` theme style to include my Feynman diagram footer, which requires some hacks to the style sheets. 

* Note: modifying the footer ends up being a little subtle because of the way the footer margins and padding are tied to the font size. The font size changes (responsive design), which means the footer parameters change with screen size. In turn, this makes it difficult to place graphical footer elements.

## My work environment

I used **netlify** to kickstart my fork of Hugo Theme Academic. This should make it easier to have my university url point to the netlify repository.

I am using the **GitHub** OS X interface to take care of synchronizing with GitHub. I use `Atom` to edit files.

I have a separate folder `\Graphic Sources`` that includes source files for Affinity Designer graphics. 

## 1. Initial Set Up

Here are the steps that I'm taking to go from the clean example site to one that is ready for hacking.

Delete:

* `\content\home\*` *except* `about.md`, `contact.md`, and `teaching.md` (template)
 
* `\content\post` , `\content\publication`, `\content\talk`

* Remove the fluff in `config.toml` ... there's a lot fo commentary. They're good to have handy, but it's distracting when the `config` file gets longer. I'll make a backup and then streamline.


## 2. Footer and Main Page Design

The biggest change is to update the footer. Since this is the part that's been giving me the most headaches.

1. Create a `<div id="THECONTENT">`. The purpose: this will be a white canvas. The `<body>` tag behind it will be dark gray to match the header and footer so that "overscrolling" will not look strange. (A) Make folder `/layouts/partials/` to include template overwrites. (B) Copy the following files from the corresponding `template` folder `footer_container.html`, `head_custom.html`, and `navbar.html`. 

2. Add the following line to the bottom of `navbar.html`: 

```html
<div id="THECONTENT"> <!-- closed in footer_container.html; see flip2019.css -->
```

3. Add the following line to the bottom of `footer_container.html`:

```html
</div> <!--  ends id=#THECONTENT from navbar.html; see flip2019.css -->
```

4. Add folder: `/static/css/` Insert file `flip2019.css` (based on `flip2018.css`) This introduces a bunch of kludges. For now, the main point of this file is to contain:

```css
footer {
  /* font-family: 'Raleway', sans-serif; */
  background-color:#333333;
}

#botbar1{
	background-color:#C1BBAB;
	margin: 0;
	padding: 0;
  position:relative;
  top: 85px;
	left: 0px;
	z-index: 9;
	height: 7px;
	width: 100%;
}

body{
  background-color:#333333;
}
```

5. Load `flip2019layout.css` by updating the `custom_css` line in `config.toml`:

```
custom_css = ['flip2019layout.css']
```

At this point, you should have a dark gray footer. Now let's get to the gray line separating the main content and the footer. This is what we call `#botbar1`. 

6. In `\layouts\partials\footer_container.html`, insert a `#botbar` div:

```html
<footer class="site-footer">
  <!-- FLIP'S NEW STUFF BELOW -->
  <div id="botbar1"></div>
  <!-- FLIP'S NEW STUFF ABOVE -->
  <div class="container">

```

The bottom line should show up at the bottom of the page. You should play with the `#botbar1` top margin in `flip2019layout.css` to see where this line ends up. There's something pressing and subtle to sort out first, though:

7. Fix the `footer` style so that it's written in `px` rather than `rem` or `em`. The problem with `rem` (relative to root) or `em` is that when the font size changes from responsive design, the padding will shift which will force you to re-calculate the top shift of `#botbar`.


```css
footer {
  /* font-family: 'Raleway', sans-serif; */
  background-color:#333333;
  /* Fixing academic.css line 979  */
  margin: 64px 0 0;
  padding: 32px 0;
}

#botbar1{
	background-color:#C1BBAB;
	margin: 0;
	padding: 0;
  position:relative;
  top: -35px;
	left: 0px;
	z-index: 9;
	height: 7px;
	width: 100%;
}
```

8. Now insert Feynman footer. Insert iamge into `/static/img/layout/feymmanfooter.png`. Here's what seems to work for `flip2019layout.css`:

```css
#feynmanfoot{
	background-size: 250px 200px;
	background-repeat:no-repeat;
	background-position:left top;
  pointer-events: none;
	margin: 0;
	padding: 0;
  position: relative;
  /* bottom: -100px;  */ /* if you place div above footer */
  bottom: 205px;
  left: 40vw;
	z-index: 200;
	height: 200px;
	width: 250px;
	text-align: right;
}
```

And here's what I put into `footer_container.html`:

```html
<footer class="site-footer">
  <!-- FLIP'S NEW STUFF BELOW -->
  <div id="botbar1"> </div>
  <div style="position: relative; width: 0; height: 0">
    <div id="feynmanfoot" style="background-image:url('{{ .Site.BaseURL }}/img/{{ .Site.Params.footmark }}');"></div>
  </div>
  <div style="height: 20px">
    <!-- JUST A SPACER -->
  </div>
  <!-- FLIP'S NEW STUFF ABOVE -->
  <div class="container">
```

(Note: there's an error in the 2018 version of my styel file where a stray `$` makes the `feymanfoot` div fail to be found on Netlify.)

## 3. Personalization

1. `config.toml`: `avatar = "profile/Flip600_sq.jpg"` (and upload the respective file)

2. In `header.html` (theme default) we have the following: So insert `icon.png` and `icon-192.png` as appropriate.
<!--add `\static\favicon.ico` In -->

```html
  <link rel="icon" type="image/png" href="{{ "/img/icon.png" | relURL }}">
  <link rel="apple-touch-icon" type="image/png" href="{{ "/img/icon-192.png" | relURL }}">
```

(You don't need to make edits to `header.html` for this.)

3. `cryptedemail.css` is a hack for displaying e-mail addresses in a way that isn't too easy for bots to scrape. Include it in the `config.toml` list of css files. It goes with `\layouts\shortcodes\flipemail.html` which provides a simple way to insert e-mail addresses. You'll also want to break up the e-mail address in `config.toml`:

```
  # email = "test@example.org"  # using cryptedmail
  email1 = "flip.tanedo" # using cryptedmail
  email2 = "ucr" # using cryptedmail
  email3 = "edu" # using cryptedmail
```

Commenting out the old e-mail removes that line in the `contact` widget. We'll use the [cryptedmail trick](https://stackoverflow.com/a/41566570). Create a `contact.html` widget in `\layouts\partials\widgets\` to 

```html
<!-- FLIP UPDATE -->
<!-- from: https://stackoverflow.com/a/41566570 -->
      
      {{ with $.Site.Params.email1 }}
      <li>
        <i class="fa-li fa fa-envelope fa-2x" aria-hidden="true"></i>
        <span id="person-email" itemprop="email">
        <!-- {{- if $autolink }}<a href="mailto:{{ . }}">{{ $.Site.Params.email2 }}</a>{{ else }}{{ . }}{{ end -}} -->
          <a data-name="{{ $.Site.Params.email1 }}" data-domain="{{ $.Site.Params.email2 }}" data-tld="{{ $.Site.Params.email3 }}" href="#" class="cryptedmail" onclick="window.location.href = 'mailto:' + this.dataset.name + '@' + this.dataset.domain + '.' + this.dataset.tld"></a>
        </span>
      </li>
      {{ end }}

<!-- /FLIP UPDATE -->
```

4. Added `flip2018.css` (introduces `comment` class, also Google calendar settings), and `flipaboutme.css` which modifies the `about` widget.

5. Added `about2.html` in `\layouts\partials\widgets` and modified `\content\home\about.md` to have the line

```md
widget = "about2"
```

## 4. Fonts 'n stuff

1. I shouldn't need to install any fonts in `head_custom.html`; do *not* do:

```html
<link href="https://fonts.googleapis.com/css?family=Titillium+Web" rel="stylesheet">
```

2. Insert `\data\fonts\flipfont.toml` and update the `config.toml` file to include

```toml
font = "flipfont"
```

To fix the e-mail font, put this in `flip2018.css`:

```css
#person-email{
  font-family: 'Titillium Web', sans-serif;
}

.style-email{
  font-family: 'Titillium Web', sans-serif;
}
```

Use `style-email` in the `flipemail.html` shortcode:

```html
<!-- in conjunction with cryptedemail.css -->
<a
  data-name="{{ .Get "first" }}"
  data-domain="{{ .Get "domain" }}"
  data-tld="{{ .Get "last" }}"
  href="#"
  class="cryptedmail"
  onclick="window.location.href = 'mailto:' + this.dataset.name + '@' + this.dataset.domain + '.' + this.dataset.tld"
  class="style-email">
</a>
```


## 5. Colors

1. Paste in `data\themes\fliptheme.toml` (this is a color theme)

2. Include it in `config.toml`

```toml
color_theme = "fliptheme"
```

## 6. Tweaking the nav bar

The only qualm I have about the navbar is that it defaults to being "tap to menu" for sizes below 1200px.  This is called the **breakpoint** of the navigation bar. 1200px exceedingly generous if you only have a few sections on your main page or if you want to use the navbar to navigate to other pages. 

### 6.a. How *not* to do it

See line 1232 of `\themes\academic\layouts\partials\css\academic.css` to tweak this:

```css
@media screen and (max-width: 1200px) {
```

I think you should be able to copy `academic.css` into a local folder (you should *not* tweak the template file in the `\themes\` directory). This, however, will supercede the entire `academic.css` style file, which may not be what you want long term if there are updates. (*Update: I can confirm that this strategy does work, though.*) Instead, we need to find a way to overwrite only the `display:block;` style.

Maybe the alternative should be `{display: inline;}`. No. There's definitely more to it than this. Looks like there's some bootstrap being called, e.g. `data-toggle`. I'm trying to sleuth this from the `navbar.html` template file.

### 6.b. The correct way to do it

* Indeed, the three calls to `<span class="icon-bar"></span>` is the triple-bar "[menu bar](https://stackoverflow.com/questions/18864657/icon-bar-in-twitter-bootstrap-navigation-bar)"; this comes from bootstrap.
* Lots of info looking at pages about the **Bootstrap** navigation bar!
 * [bootstrapius](https://bootstrapious.com/p/bootstrap-navbar#navbar-collapse) 
 * [love2dev](https://love2dev.com/blog/bootstrap-navbar/)

From bootstrapius:
> The navbar collapse is the component which wraps the remain components of the navbar, such as navbar menu, navbar buttons, forms... etc. In other words, it wraps all navbar components except `.navbar-header`.

> It's defined by the two classes `.collapse navbar-collapse`. This box should have an `id`, this id must be identical to the toggle button `data-target=""` value.

> Contrary to the navbar toggle button, *navbar collapse is visible by default on larger screens and hidden on smaller ones*. On smaller screens, it is switched from hidden to visible and vice versa by clicking navbar toggle button.

The thing I'm looking for is called the **breakpoint**. Use that in my search queries. Maybe [this](https://stackoverflow.com/questions/19827605/change-bootstrap-navbar-collapse-breakpoint-without-using-less). The "[2018 update](https://stackoverflow.com/a/36289507/4812646)" answer is the one that I want:

> Changing the navbar breakpoint is easier in Bootstrap 4 using the navbar-expand-* classes:

```html
<nav class="navbar fixed-top navbar-expand-sm">..</nav>
```

The options are

* `navbar-expand-sm`: mobile menu on xs screens <576px
* `navbar-expand-md`: mobile menu on sm screens <768px
* `navbar-expand-lg`: mobile menu on md screens <992px
* `navbar-expand-xl`: mobile menu on lg screens <1200px
* `navbar-expand`: never use mobile menu
* *(no expand class)* = always use mobile menu

See [this example](https://www.codeply.com/go/n4Pn4aB695). So what we really need is to hack is `\layouts\partials\navbar.html`, which we have already modified when we added the `<div id="THECONTENT">` line at the end of the file.

On the top line, change

```html
<nav class="navbar navbar-default navbar-fixed-top" id="navbar-main">
```

to

```html
<nav class="navbar navbar-default navbar-fixed-top navbar-expand-sm" id="navbar-main">
```

**Unfortunately**, Bootstrap 4 is [*not* supported yet](https://github.com/gcushen/hugo-academic/issues/509) with Hugo Theme Academic (as of June 2018). 

Fortunately, the "[2018 update](https://stackoverflow.com/a/36289507/4812646)" answer has a Bootstrap 3 guide:

```css
@media (max-width: 991px) {
  .navbar-header {
      float: none;
  }
  .navbar-left,.navbar-right {
      float: none !important;
  }
  .navbar-toggle {
      display: block;
  }
  .navbar-collapse {
      border-top: 1px solid transparent;
      box-shadow: inset 0 1px 0 rgba(255,255,255,0.1);
  }
  .navbar-fixed-top {
      top: 0;
      border-width: 0 0 1px;
  }
  .navbar-collapse.collapse {
      display: none!important;
  }
  .navbar-nav {
      float: none!important;
      margin-top: 7.5px;
  }
  .navbar-nav>li {
      float: none;
  }
  .navbar-nav>li>a {
      padding-top: 10px;
      padding-bottom: 10px;
  }
  .collapse.in{
      display:block !important;
  }
}
```

Where one can change `991px` to any number over `768px`. The case for smaller breakpoints is discussed [here](https://stackoverflow.com/questions/42724884/bootstrap-navbar-collapse-point-768px/42725215#42725215). *Damn*, this doesn't seem to work.

Okay. I give up on this. I should just move on.

Some other notes

* It's weird that it works if I just make a local copy of `academic.css`. The real problem is *undoing* the navbar stuff in `academic.css`. Ideally one should put something in

```css
@media (min-width: 800px) and (max-width: 1200px)
```

in the `flipnav.css` file that "undoes" the effect of the `academic.css` `@media` piece in the navbar section. To do this, I have to go through the `academic.css` `@media` part and undo each declaration by returning it to its `academic.css` *or* bootstrap default value (if its not touched in `academic.css`. This is a total pain.

**Work in Progress**: I think the way to do this is to   

1. Look at the **navbar** section of `academic.css`, specifically the `@media (max-width: 1200px)` portion that describes what to modify when the screen is small.  

2. You need to compare this to (a) the default behavior in `academic.css` when the screen is large, which is sometimes (b) the behavior specified in **bootstrap 3**.

This is a bit of an annoying task. A better solution would be to move the whole platform to bootstrap 4, but that's a large undertaking. For now I'm going to leave the default 1200px breakpoint as is.

## 7. More formatting tweaks

### Tweaking job title font

In `/layouts/partials/widgets/about.html` around line 19 (`<div class="portrait-title">`), did a minor font change for the job title and organization:

```html
      <div class="portrait-title">
        <h2 itemprop="name">{{ $.Site.Params.name }}</h2>
        {{ with $.Site.Params.role }}
          <!-- <h3 itemprop="jobTitle">{{ . }}</h3> -->
          <h3 itemprop="jobTitle" style="font-family:Lato;">{{ . }}</h3>
        {{ end }}
```

* Make the navbar opaque. Opacity is in the `rgba` css designator. I'm going to put this in `flipnav.css`, which I originally made to modify the beakpoint.

```css
.navbar-default{
    background-color:rgba(0, 0, 0, 0.75);
}
 
.navbar-default .navbar-brand {
  color: white;
}
 
.navbar-default .navbar-nav li a{
    color: white;
}
 
.navbar-default .navbar-nav li a:hover{
    background-color: #333333;
}
```

### Triple Column in Footer

Footer triple column, inspired by [hugo-initio](https://themes.gohugo.io/theme/hugo-initio/) theme by Miguel Simoni. This is a fairly simple one, use a *bootstrap* grid. We have to modify `\layouts\partials\footer_container.html`:

The `div` layout is as follows:  
`footer site-footer` > `div botbar1` > `div feynmanfoot` > `div` (spacer) > `div container`  

In the default academic theme, the next layer is a `p powered-by`. What we're going to do is replace that with a `div row` > `div col-sm-4`.

Now we should define some logos to put into the columns. I'm adding the information under the `#FOOTER PARAMETERS` part of `config.toml`. 

Here's what it looks like:

footer_container.html:  

```html
<!-- FLIP NEW STUFF: Three columns -->
      <div class="row" id="footer-columns">
        <div class="col-sm-4" id="footer-col-1">
          <img src="{{ $.Site.BaseURL }}img/{{ $.Site.Params.stacklogo }}">
        </div>
        <div class="col-sm-4" id="footer-col-1">
          <!-- <img src="{{ $.Site.BaseURL }}img/{{ $.Site.Params.deptlogo }}"> -->
          {{ $.Site.Params.legalblurb }}
        </div>
        <div class="col-sm-4" id="footer-col-1">
          Powered by the
          <a href="https://gohugo.io" target="_blank" rel="noopener">Hugo</a> engine
          <!-- <br/ > -->
          Adapted from <a href="https://sourcethemes.com/academic/" target="_blank" rel="noopener">Academic</a>.
          <!-- <br/ > -->
          &copy Philip (Flip) Tanedo 2018
        </div>
      </div>
```

config.toml:

```
  # FOOTER PARAMETERS
  footmark = "layout/feynmanfooter.png"
  stacklogo = "logo/UCR_PhysicsAndAstro_Logo_fordarkBG3.png"
  legalblurb = "All opinions expressed here are my own and do not necessarily represent those of any other agencies or groups."
```

flip2018layout.css:  

```
/*  FOOTER 3 Column  */

#footer-columns{
  padding-top: 35px;
  margin-right: 20px;
  margin-left: 20px;
  font-size: .75rem;
  font-family:Lato;
}

#footer-col-1{
  padding-bottom: 25px;
}

#footer-columns img{
  max-width: 200px;
  opacity: .5;
}
```

### Footer

Widget pages (like the home page) use the `nth-of-type` CSS structure to create alternating styles. The final widget may end up with a gray background. 

Here's how `academic.css` does it:

```css
.home-section {
  background-color: {{ .Get "home_section_odd" }};
  padding: 110px 0 110px 0;
  animation: intro 0.3s both;
  animation-delay: 0.15s;
}

.home-section:first-of-type {
  padding-top: 50px;
}

.home-section:nth-of-type(even) {
  background-color: {{ .Get "home_section_even" }};
}
```

We have to adapt the footer background in case an "even" numbered widget is last and thus has a gray background. This is something we introduced in the `#THECONTENT` divider and is controlled in flip2019layout.css. This also controls all the even numbered guys.

I can fix this in flip2018.css by removing the transparency:

```css
/* to make "white bg" transparent */
.home-section {
  /* background-color: transparent; */
  /* background-color: {{ .Get "home_section_odd" }}; */
  z-index: 1;
}

```
... but this gits rid of the transparent background and doesn't really solve the problem. It just means that you can hard code the background color by setting what `#THECONTENT` color should be. 

I think the way to fix this is to apply some Hugo magic to `navbar.html` and have the `<div id="THECONTENT">` line (the last one) set a background color that depends on the number of widgets. Here's a pretty good attempt:

```html
<!-- BASED ON widget_page.html -->
  {{ if .IsHome }}
    {{ .Scratch.Set "section" "home" }}
  {{ else }}
    {{ .Scratch.Set "section" .Section }}
  {{ end }}
  {{ $section := .Scratch.Get "section" }}

  {{ $page := where (where .Data.Pages "Section" $section) ".Params.active" "!=" false }}
  {{ $pageCount := len $page }}
  {{ if modBool $pageCount 2}}
    <div id="THECONTENT" style="background-color: rgb(247, 247, 247);">
  {{ else }}
    <div id="THECONTENT" style="background-color: rgb(255, 255, 255);">
  {{ end }}
```

The problem with this one is that (1) I still need fix the widget background colors (they're incompatible with transparency) and (2) I get a weird extra space at the top. Hugo seems to be inserting an implicit `<br>` or something.

**For now** I think the best answer is to just have odd numbers of widgets. Bit of a shame, but this is something that I can "deep dive" into when there's time.


## 8. Content

### About Widget

We'd like to put a watermark.

In flip2018.css:  

```css
/* to make "white bg" transparent */
.home-section {
  background-color: transparent;
  z-index: 1;
}

#watermark{
	/*background-image:url('http://physics.ucr.edu/~flip/images/BG_Bundle2.jpg');*/
	background-size: 700px 350px;
	background-repeat:no-repeat;
	background-position:left top;
	margin: 0;
	padding: 0;
	position: absolute;
	top: 30px;
	left: 0px;
  pointer-events: none;
	/*z-index: 0;*/
	height: 100%;
	width: 100%;
	text-align: right;
	opacity: 0.1;
}
```

Now hack the top of `layouts\partials\navbar.html` (which we already modified for the navbr):  

```html
<!-- FOR WATERMARK -->
<!-- Put it here for convenience -->
<div id="watermark" style="background-image:url('{{ $.Site.BaseURL }}/img/{{ .Site.Params.watermark }}');"></div>
<!--  -->
<nav class="navbar navbar-default navbar-fixed-top" id="navbar-main">
```

Note: in past iterations I hacked `header.html`, but this is annoying since it's another template default file that we're over-riding for a small reason. Instead, it's easy to see that `header.html` is always immediately followed by `navbar.html` and, further, that the place to insert the watermark is just after the `<body>` opening, which is the last line of `header.html`. Thus placing the watermark `<div>` as the first line of `navbar.html` gives the identical effect.

### CV Widget

Create a `\layouts\partials\widgets\CV.html` file based on the `academic\layouts\partials\widgets\custom.html` template. Create a `/content/home/CV.md` file based on the `/content/home/teaching.md` tempalte.

In `CV.md`, first point to the `CV.html` widget template: `widget = "CV"`.

I started by 'cleaning' `CV.html` by removing the "no left column" formatting in case there's no widget title. There will always be a widget title:

```html
<!-- Custom widget -->
<div class="row">


  <div class="col-xs-12 col-md-4 section-heading">
    {{ if $page.Title }}
    <h1>{{ with $page.Title }}{{ . | markdownify }}{{ end }}</h1>
    {{ with $page.Params.subtitle }}<p>{{ . | markdownify }}</p>{{ end }}
    {{ end }}
  </div>
  <div class="col-xs-12 col-md-8">
    {{ $page.Content }}
  </div>


</div> <!-- row -->
```

#### Add "download pdf" option:

In `CV.html` under `<div class="col-xs-12 col-md-4 section-heading">`:

```html
    <p>
    <a class="btn btn-primary btn-outline btn-xs" href="{{ $page.Params.cv_pdf }}">
      Download Complete CV
    </a>
    </p>
```

In the header of `CV.md`:

```md
# CV location
cv_pdf = "./files/Tanedo.pdf"
```

In the file structure: `/static/files/Tanedo.pdf`.


#### The group logo

In `flip2018.css`:  

```css
/*  for CV WIDGET  */

.sidebarpic{
  /* sets max size  */
  width: 250px;
  height: 250px;
  text-align: center;
  margin: 0 auto;
}
```

In `CV.md`:  

```md
# Group Logo
group_logo = "./img/logo/UCRHEP_03.png"
```

In `CV.html`:  

```html
    {{ if $page.Params.group_logo }}
    <p>
      <img class="sidebarpic" src="{{ $page.Params.group_logo }}">
      <meta itemprop="image" content="{{ $page.Params.group_logo }}">
    </p>
    {{ end }}
```

Note that I was a bit more clever with the go `if` statement here. That's probably good practice.


#### Filling it in

`CV.html`:  

```html
  <div class="col-xs-12 col-md-8">
    {{ $page.Content }}


    <!-- ADAPTED FROM about.html -->
    <div class="row" style="margin-bottom: 10px;">

          {{ with $page.Params.interests }}
          <div class="col-sm-5">
            <h3>{{ i18n "interests" | markdownify }}</h3>
            <ul class="ul-interests">
              {{ range .interests }}
              <li>{{ . }}</li>
              {{ end }}
            </ul>
          </div>
          {{ end }}

          {{ with $page.Params.education }}
          <div class="col-sm-7">
            <h3>{{ i18n "education" | markdownify }}</h3>
            <!-- <ul class="ul-edu fa-ul"> -->
              {{ range .courses }}
              <!-- <li> -->
                {{ if .logo }}
                <img src="{{ $.Site.BaseURL }}img/{{ .logo }}" style="height:1rem; float: left; padding-right: 10px;">
                {{ else }}
                <i class="fa-li fa fa-graduation-cap"></i>
                {{ end }}
                <div class="description">
                  <!-- <p class="course"> -->
                    {{ .course_short }}
                    {{ with .institution_short }}, {{ . }}{{ end }}
                    {{ with .year }}({{ . }}){{ end }}
                    <br />
                  <!-- </p> -->
                  <!-- <p class="course">{{ .course }}{{ with .year }}, {{ . }}{{ end }}</p> -->
                  <!-- <p class="institution">{{ .institution }}</p> -->
                </div>
              <!-- </li> -->
              {{ end }}
            <!-- </ul> -->
          </div>
          {{ end }}

      </div> <!-- end row of CV items -->

      {{ with $page.Params.service }}
        <p style="font-size: .8rem;">
          <b>Service</b>:
          {{ range .service }}
          {{ . }} ,
          {{ end }}
        </p>
      {{ end }}


  </div> <!-- col-xs-12 col-md-8 -->
```

CV.md:    

```md
# List your academic interests.
[interests]
  interests = [
    "Dark Matter",
    "Composite Higgs",
    "Extra Dimensions/SUSY",
    "Astro/cosmo-particle"
  ]

# List your qualifications (such as academic degrees).
[[education.courses]]
  course = "PhD in Physics"
  course_short = "PhD"
  institution = "Cornell University"
  institution_short = "Cornell"
  year = 2013
  logo = "/logo/icon_Co.png"

[[education.courses]]
  course = "MSc in Physics"
  course_short = "MSc"
  institution = "Durham University / IPPP"
  institution_short = "Durham/IPPP"
  year = 2008
  logo = "/logo/icon_D.png"

[[education.courses]]
  course = "MASt in Mathematics"
  course_short = "MASt"
  institution = "Cambridge University"
  institution_short = "Cambridge"
  year = 2007
  logo = "/logo/icon_Ca.png"

[[education.courses]]
  course = "BS in Physics & Mathematics"
  course_short = "BS"
  institution = "Stanford University"
  institution_short = "Stanford"
  year = 2008
  logo = "/logo/icon_S.png"

# List your academic interests.
[service]
  service = [
    "Open House Committee (chair)",
    "Website Committee (chair)",
    "Graduate Diversity Committee",
    "Graduate Student Mentor (Graduate Division)"
  ]
```


### Research Widget

This would be great for a carousel. Cushen [recommends](https://github.com/gcushen/hugo-academic/issues/399): [Slick](http://kenwheeler.github.io/slick/).

**Setting Up Slick**:  
* Note: header.html has the line that ranges over `custom_css`, whereas footer.html ranges over `custom_js`. This is relevant for slick.

* `slick.css` needs to be in the `<head>`, which works if you put it in the `custom_css` list in `config.toml`. 

* `slick.js` needs to be before the closing `</body>` tag, which means that having it in the footer is fine, *except*...

* ... that it has to be called *after* **jQuery** (version > 1.7). `academic` already calls jQuery in footer.html, and further that the `custom_js` scripts are loaded *after* jQuery is called. We should be safe putting `slick.js` in the `custom_js` list of `config.toml`. (You'll need to make a `\static\js\` folder if you haven't already.)  **Update**: the current version of **jQuery** being called in `academic` is an old one. We'll have to call a newer one. ((Hopefully all this stuff gets cleaned up with the next version of `academic`.))

* A few others: in the `css` directory, include `ajax-loader.gif` and the slick `fonts` directory. This clutters things up a little, but we can clean it up later. (I'm not even sure how necessary this is, but it was throwing up errors in my browser.)

* I made a `flipslick.css` and a `flipslick.js` are based on pieces of code in the slick examples. 

`flipslick.css` is: (use `.slider`'s `margin` to change the spacing above and below the slider.)

```css
.slider {
        width: 90%;
        margin: 10px auto;
    }

    .slick-slide {
      margin: 0px 20px;
    }

    .slick-slide img {
      width: 100%;
    }

    .slick-prev:before,
    .slick-next:before {
      color: black;
    }

    .slick-slide {
      transition: all ease-in-out .3s;
      opacity: .2;
    }

    .slick-active {
      opacity: .5;
    }

    .slick-current {
      opacity: 1;
```

* First, make a local copy of `https://code.jquery.com/jquery-2.2.0.min.js`, put it in the `\js\` folder and make sure `config.toml` calls it *before* `slick.js`. 

* Then make a `flipslick.js` in the same folder and make sure `config.toml` calls it *after* `slick.js`. The content of `flipslick.js` are the details of the slider. We'll use


```js
$(document).on('ready', function() {
  $('.single-item').slick({
    dots: true,
    infinite: true,
    centerMode: true}
  );
});
```

* So far so good. You can now test this by inserting the following into the `research.html` template:

```html
<!--  -->

    <!-- <div class="center slider"> -->
    <div class="single-item slider">
    <div>
      <img src="http://placehold.it/350x100?text=1">
    </div>
    <div>
      <img src="http://placehold.it/350x100?text=2">
    </div>
    <div>
      <img src="http://placehold.it/350x100?text=3">
    </div>
    <div>
      <img src="http://placehold.it/350x100?text=4">
    </div>
    <div>
      <img src="http://placehold.it/350x100?text=5">
    </div>
    <div>
      <img src="http://placehold.it/350x100?text=6">
    </div>
    <div>
      <img src="http://placehold.it/350x100?text=7">
    </div>
    <div>
      <img src="http://placehold.it/350x100?text=8">
    </div>
    <div>
      <img src="http://placehold.it/350x100?text=9">
    </div>
    <div>
      <img src="http://placehold.it/350x100?text=10">
    </div>
  </div>



<!--  -->
```

* If that worked, we'll now have to replace that test code with **hugo** commands that scan over `research.md`.

Here's how I did it:

```html
<!--  -->

    <div class="single-item slider">
    {{ with $page.Params.research }}
      {{ range .projects }}
      <div>

        <img src="{{ $.Site.BaseURL }}img/{{ .image }}">
        <!-- <img src="{{ .image }}"> -->

        <div style = "font-family:Lato; font-size: .7rem;">
        {{ if .caption }}
          {{ .caption | markdownify }}
        {{ end }}
        </div>
      </div>
      {{ end }}
    {{ end }}
  </div>


<!--  -->
```

* Here's the bonus: make it a shortcode

## License

*Copied from original Academic Kickstart `README.md`*

Copyright 2017 [George Cushen](https://georgecushen.com).

Released under the [MIT](https://github.com/sourcethemes/academic-kickstart/blob/master/LICENSE.md) license.

[![Analytics](https://ga-beacon.appspot.com/UA-78646709-2/academic-kickstart/readme?pixel)](https://github.com/igrigorik/ga-beacon)
