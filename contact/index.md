---
comments: false
date: 2018-03-16
layout: page
title: Contact Me
---


## Contact Me

<form action="https://formspree.io/sebastiangrundet@hotmail.dk" method="post">
	<label for="name">Name</label>    
	<input type="text" id="name" name="name" class="full-width"><br>
	<label for="email">Email Address</label>
	<input type="email" id="email" name="_replyto" class="full-width"><br>
	<label for="message">Message</label>
	<textarea name="message" id="message" cols="30" rows="10" class="full-width"></textarea><br>
	<div markdown="0"><input type="submit" value="Send" class="btn btn-success" /></div>
</form>



<link rel='stylesheet' type='text/css' href='{{site.url}}/assets/css/contact.css' />


<script>
	jQuery(document).ready(function($){
	if( $('.floating-labels').length > 0 ) floatLabels();

	function floatLabels() {
		var inputFields = $('.floating-labels .cd-label').next();
		inputFields.each(function(){
			var singleInput = $(this);
			//check if user is filling one of the form fields
			checkVal(singleInput);
			singleInput.on('change keyup', function(){
				checkVal(singleInput);
			});
		});
	}

	function checkVal(inputField) {
		( inputField.val() == '' ) ? inputField.prev('.cd-label').removeClass('float') : inputField.prev('.cd-label').addClass('float');
	}
	});
</script>