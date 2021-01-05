---
layout: page
title: Books
permalink: /books/
---
<div>
<h3>Programming</h3>
{% for file in site.static_files %}
	{% if file.programming %}
		<a href="{{file.path}}">{{file.basename}}</a><br>
	{% endif %}
{% endfor %}
</div>
<br>
<div>
<h3>Life</h3>
{% for file in site.static_files %}
	{% if file.life %}
		<a href="{{file.path}}">{{file.basename}}</a><br>
	{% endif %}
{% endfor %}
</div>
<br>
<div>
<h3>Business</h3>
{% for file in site.static_files %}
	{% if file.business %}
		<a href="{{file.path}}">{{file.basename}}</a><br>
	{% endif %}
{% endfor %}
</div>
<br>
<div>
<h3>Skills</h3>
{% for file in site.static_files %}
	{% if file.skills %}
		<a href="{{file.path}}">{{file.basename}}</a><br>
	{% endif %}
{% endfor %}
</div>
<br>
<div>
<h3>Music</h3>
{% for file in site.static_files %}
	{% if file.music %}
		<a href="{{file.path}}">{{file.basename}}</a><br>
	{% endif %}
{% endfor %}
</div>
<br>
<div>
<h3>Aspergers</h3>
{% for file in site.static_files %}
	{% if file.aspergers %}
		<a href="{{file.path}}">{{file.basename}}</a><br>
	{% endif %}
{% endfor %}
</div>
