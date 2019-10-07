## GithubDocSync


This page demonstrates a method of getting documentation from readme.md (or other docs) from github and displaying it in github pages in order to maintain documentation in only one place. This method uses Javascript and the Github API to fetch the content in the browser when the page loads.

This is the markdown for the JQuery example. For the no-JQuery example markdown, see [joJQuery.md](https://github.com/bradrhodes/GithubDocSync/blob/master/noJQuery.md)

### Motivation

This is alternative method to those provided by coryg89's [Docsync](http://coryg89.github.io/docsync/) project and bebraw's [readme2gh](http://www.nixtu.info/2012/09/readme2gh-keeps-your-github-readmemd.html) project.

Both approaches are fine but coryg89's is dependent upon a bash script that is installed as a git hook. A problem with this is that if you make a quick change on github, rather than using a git client, your change won't be reflected on github pages. Bebraw's approach is dependent upon a Ruby script. 

I wanted to take an approach that always worked, no matter how you change/update the documentation and one that didn't require any sort of out-of-band scripting or check-in policies, etc.

I'll describe how to do this with the readme.md file on Github but it should be easily adaptable to any other documenation you have in your repo.

### Basic Proces

1. Use the Github API and JSONP to fetch the readme.md from Github.
2. Decode the base64 content in the response from Github API.
3. Parse/Render the markdown to HTML
4. Display the rendered HTML

### Step-by-step
- add `base64.js` to your `/javascripts` directory in your `gh-pages` branch   
   - You can get it from [Web Toolkit](http://www.webtoolkit.info/javascript-base64.html) or from the [javascripts](https://github.com/bradrhodes/GithubDocSync/tree/gh-pages/javascripts) directory of the `gh-pages` branch of GithubDocSync   
- add `marked.js` to your `/javascripts` directory in your `gh-pages` branch
   - You can get it from the `/lib` directory of chjj's [marked](https://github.com/chjj/marked/tree/master/lib) project or from the [javascripts](https://github.com/bradrhodes/GithubDocSync/tree/gh-pages/javascripts) 
- open `index.html` in an editor and add the following `<script>` references to the `<head>`
```html   
<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.7.1/jquery.min.js"></script>
<script src="javascripts/base64.js"></script>
<script src="javascripts/marked.js"></script>
```
- also in `index.html` add the following javascript to the `<head>` (replace githubApiUrl with the appropriate value for the file you want on Github)
```html
<script type="text/javascript">
	  var githubApiUrl = 'https://api.github.com/repos/bradrhodes/GithubDocSync/contents/README.md';
	  var contentContainerId = "#readmeContent";
	
	  $.ajax({
	    url: githubApiUrl,
	    dataType: 'jsonp',
	    success: function(response)
	    {
	      var encodedContent = response.data.content;
	      var markdownText = Base64.decode(encodedContent);
	      var renderedHtml = marked(markdownText);
	
	      $(contentContainerId).html(renderedHtml);
	    }
	  });
</script>
```
- add a block-level container with id='#readmeContent'
	- ex. `<div id="readmeContent"></div>`
- save, add, commit and push all the changes to your `gh-pages` branch
   


