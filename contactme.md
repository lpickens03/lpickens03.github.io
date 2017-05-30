---
layout: form
title: Contact Me
quote: Security is not a product; it itself is a process. ... If you think technology can solve you security problems, then you don't understand the problems and you don't understand the technology. 
quote-author: Bruce Schneier
---
<form id="contactform" name="contactme" method="POST">
	<div class="row control-group">
        <div class="form-group col-xs-12 floating-label-form-group controls">
            <label>Name</label>
            <input type="text" class="form-control" placeholder="Name" name="name" required data-validation-required-message="Please enter your name.">
            <p class="help-block text-danger"></p>
        </div>
    </div>
    <div class="row control-group">
        <div class="form-group col-xs-12 floating-label-form-group controls">
            <label>Email Address</label>
            <input type="email" class="form-control" placeholder="Email Address" name="_replyto" required data-validation-required-message="Please enter your email address.">
            <p class="help-block text-danger"></p>
        </div>
    </div>
    <div class="row control-group">
        <div class="form-group col-xs-12 floating-label-form-group controls">
            <label>Message</label>
            <textarea rows="5" class="form-control" placeholder="Message" name="message" required data-validation-required-message="Please enter a message."></textarea>
            <p class="help-block text-danger"></p>
        </div>
    </div>
    <input type="text" name="_gotcha" style="display:none" >
    <input type="hidden" name="_next" value="/thankyou" />
    <input type="hidden" name="_subject" value="Website Contact Form" />
    <br>
    <div id="success"></div>
    <div class="row">
    	<input class="btn-contactme" type="submit" value="Send">
    </div>
</form>

<script>
    var contactform =  document.getElementById('contactform');
    contactform.setAttribute('action', '//formspree.io/' + 'contact' + '@' + 'laurapickens' + '.' + 'net');
</script>