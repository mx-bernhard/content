---
title: Client-side form validation
slug: Learn/Forms/Form_validation
tags:
  - Beginner
  - Example
  - Forms
  - Guide
  - HTML
  - JavaScript
  - Learn
  - Web
  - regex
---
<div>{{LearnSidebar}}</div>

<div>{{PreviousMenuNext("Learn/Forms/UI_pseudo-classes", "Learn/Forms/Sending_and_retrieving_form_data", "Learn/HTML/Forms")}}</div>

<p>Before submitting data to the server, it is important to ensure all required form controls are filled out, in the correct format. This is called <strong>client-side form validation</strong>, and helps ensure data submitted matches the requirements set forth in the various form controls. This article leads you through basic concepts and examples of client-side form validation.</p>

<table>
 <tbody>
  <tr>
   <th scope="row">Prerequisites:</th>
   <td>Computer literacy, a reasonable understanding of <a href="/en-US/docs/Learn/HTML">HTML</a>, <a href="/en-US/docs/Learn/CSS">CSS</a>, and <a href="/en-US/docs/Learn/JavaScript">JavaScript</a>.</td>
  </tr>
  <tr>
   <th scope="row">Objective:</th>
   <td>To understand what client-side form validation is, why it's important, and how to apply various techniques to implement it.</td>
  </tr>
 </tbody>
</table>

<p>Client-side validation is an initial check and an important feature of good user experience; by catching invalid data on the client-side, the user can fix it straight away. If it gets to the server and is then rejected, a noticeable delay is caused by a round trip to the server and then back to the client-side to tell the user to fix their data.</p>

<p>However, client-side validation <em>should not be considered</em> an exhaustive security measure! Your apps should always perform security checks on any form-submitted data on the <em>server-side</em> <strong>as well</strong> as the client-side, because client-side validation is too easy to bypass, so malicious users can still easily send bad data through to your server. Read <a href="/en-US/docs/Learn/Server-side/First_steps/Website_security">Website security</a> for an idea of what <em>could</em> happen; implementing server-side validation is somewhat beyond the scope of this module, but you should bear it in mind.</p>

<h2 id="What_is_form_validation">What is form validation?</h2>

<p>Go to any popular site with a registration form, and you will notice that they provide feedback when you don't enter your data in the format they are expecting. You'll get messages such as:</p>

<ul>
 <li>"This field is required" (You can't leave this field blank).</li>
 <li>"Please enter your phone number in the format xxx-xxxx" (A specific data format is required for it to be considered valid).</li>
 <li>"Please enter a valid email address" (the data you entered is not in the right format).</li>
 <li>"Your password needs to be between 8 and 30 characters long and contain one uppercase letter, one symbol, and a number." (A very specific data format is required for your data).</li>
</ul>

<p>This is called <strong>form validation</strong>. When you enter data, the browser and/or the web server will check to see that the data is in the correct format and within the constraints set by the application. Validation done in the browser is called <strong>client-side</strong> validation, while validation done on the server is called <strong>server-side</strong> validation. In this chapter we are focusing on client-side validation.</p>

<p>If the information is correctly formatted, the application allows the data to be submitted to the server and (usually) saved in a database; if the information isn't correctly formatted, it gives the user an error message explaining what needs to be corrected, and lets them try again.</p>

<p>We want to make filling out web forms as easy as possible. So why do we insist on validating our forms? There are three main reasons:</p>

<ul>
 <li><strong>We want to get the right data, in the right format.</strong> Our applications won't work properly if our users' data is stored in the wrong format, is incorrect, or is omitted altogether.</li>
 <li><strong>We want to protect our users' data</strong>. Forcing our users to enter secure passwords makes it easier to protect their account information.</li>
 <li><strong>We want to protect ourselves</strong>. There are many ways that malicious users can misuse unprotected forms to damage the application (see <a href="/en-US/docs/Learn/Server-side/First_steps/Website_security">Website security</a>).<br>
  {{warning("Never trust data passed to your server from the client. Even if your form is validating correctly and preventing malformed input on the client-side, a malicious user can still alter the network request.")}}</li>
</ul>

<h2 id="Different_types_of_client-side_validation">Different types of client-side validation</h2>

<p>There are two different types of client-side validation that you'll encounter on the web:</p>

<ul>
 <li><strong>Built-in form validation</strong> uses HTML5 form validation features, which we've discussed in many places throughout this module. This validation generally doesn't require much JavaScript. Built-in form validation has better performance than JavaScript, but it is not as customizable as JavaScript validation.</li>
 <li><strong>JavaScript</strong> validation is coded using JavaScript. This validation is completely customizable, but you need to create it all (or use a library).</li>
</ul>

<h2 id="Using_built-in_form_validation">Using built-in form validation</h2>

<p>One of the most significant features of <a href="/en-US/docs/Learn/Forms/HTML5_input_types">HTML5 form controls</a> is the ability to validate most user data without relying on JavaScript. This is done by using validation attributes on form elements. We've seen many of these earlier in the course, but to recap:</p>

<ul>
 <li><code><a href="/en-US/docs/Web/HTML/Attributes/required">required</a></code>: Specifies whether a form field needs to be filled in before the form can be submitted.</li>
 <li><code><a href="/en-US/docs/Web/HTML/Attributes/minlength">minlength</a></code> and <code><a href="/en-US/docs/Web/HTML/Attributes/maxlength">maxlength</a></code>: Specifies the minimum and maximum length of textual data (strings)</li>
 <li><code><a href="/en-US/docs/Web/HTML/Attributes/min">min</a></code> and <code><a href="/en-US/docs/Web/HTML/Attributes/max">max</a></code>: Specifies the minimum and maximum values of numerical input types</li>
 <li><code>type</code>: Specifies whether the data needs to be a number, an email address, or some other specific preset type. </li>
 <li><code><a href="/en-US/docs/Web/HTML/Attributes/pattern">pattern</a></code>: Specifies a <a href="/en-US/docs/Web/JavaScript/Guide/Regular_Expressions">regular expression</a> that defines a pattern the entered data needs to follow.</li>
</ul>

<p>If the data entered in a form field follows all of the rules specified by the above attributes, it is considered valid. If not, it is considered invalid.</p>

<p>When an element is valid, the following things are true:</p>

<ul>
 <li>The element matches the {{cssxref(":valid")}} CSS pseudo-class, which lets you apply a specific style to valid elements.</li>
 <li>If the user tries to send the data, the browser will submit the form, provided there is nothing else stopping it from doing so (e.g., JavaScript).</li>
</ul>

<p>When an element is invalid, the following things are true:</p>

<ul>
 <li>The element matches the {{cssxref(":invalid")}} CSS pseudo-class, and sometimes other UI pseudo-classes (e.g., {{cssxref(":out-of-range")}}) depending on the error, which lets you apply a specific style to invalid elements.</li>
 <li>If the user tries to send the data, the browser will block the form and display an error message.</li>
</ul>

<div class="notecard note">
<p><strong>Note:</strong> There are several errors that will prevent the form from being submitted, including a {{domxref('validityState.badInput', 'badInput')}}, {{domxref('validityState.patternMismatch','patternMismatch')}}, {{domxref('validityState.rangeOverflow','rangeOverflow')}} or {{domxref('validityState.rangeUnderflow','rangeUnderflow')}}, {{domxref('validityState.stepMismatch','stepMismatch')}}, {{domxref('validityState.tooLong','tooLong')}} or {{domxref('validityState.tooShort','tooShort')}}, {{domxref('validityState.typeMismatch','typeMismatch')}}, {{domxref('validityState.valueMissing','valueMissing')}}, or a {{domxref('validityState.customError','customError')}}.</p>
</div>

<h2 id="Built-in_form_validation_examples">Built-in form validation examples</h2>

<p>In this section, we'll test out some of the attributes that we discussed above.</p>

<h3 id="Simple_start_file">Simple start file</h3>

<p>Let's start with a simple example: an input that allows you to choose whether you prefer a banana or a cherry. This example involves a simple text {{HTMLElement("input")}} with an associated {{htmlelement("label")}} and a submit {{htmlelement("button")}}. Find the source code on GitHub at <a href="https://github.com/mdn/learning-area/blob/master/html/forms/form-validation/fruit-start.html">fruit-start.html</a> and a live example below.</p>

<pre class="brush: html">&lt;form&gt;
  &lt;label for="choose"&gt;Would you prefer a banana or cherry?&lt;/label&gt;
  &lt;input id="choose" name="i_like"&gt;
  &lt;button&gt;Submit&lt;/button&gt;
&lt;/form&gt;</pre>

<pre class="brush: css">input:invalid {
  border: 2px dashed red;
}

input:valid {
  border: 2px solid black;
}</pre>

<p>{{EmbedLiveSample("Simple_start_file", "100%", 80)}}</p>

<p>To begin, make a copy of <code>fruit-start.html</code> in a new directory on your hard drive.</p>

<h3 id="The_required_attribute">The required attribute</h3>

<p>The simplest HTML5 validation feature is the <code><a href="/en-US/docs/Web/HTML/Attributes/required">required</a></code> attribute. To make an input mandatory, add this attribute to the element. When this attribute is set, the element matches the {{cssxref(':required')}} UI pseudo-class and the form won't submit, displaying an error message on submission when the input is empty. While empty, the input will also be considered invalid, matching the {{cssxref(':invalid')}} UI pseudo-class.</p>

<p>Add a <code>required</code> attribute to your input, as shown below.</p>

<pre class="brush: html">&lt;form&gt;
  &lt;label for="choose"&gt;Would you prefer a banana or cherry? (required)&lt;/label&gt;
  &lt;input id="choose" name="i_like" required&gt;
  &lt;button&gt;Submit&lt;/button&gt;
&lt;/form&gt;</pre>

<p>Note the CSS that is included in the example file:</p>

<pre class="brush: css">input:invalid {
  border: 2px dashed red;
}

input:invalid:required {
  background-image: linear-gradient(to right, pink, lightgreen);
}

input:valid {
  border: 2px solid black;
}</pre>

<p>This CSS causes the input to have a red dashed border when it is invalid and a more subtle solid black border when valid. We also added a background gradient when the input is required <em>and</em> invalid. Try out the new behavior in the example below:</p>

<p>{{EmbedLiveSample("The_required_attribute", "100%", 80)}}</p>

<div class="notecard note">
<p><strong>Note:</strong> You can find this example live on GitHub as <a href="https://mdn.github.io/learning-area/html/forms/form-validation/fruit-required.html">fruit-validation.html</a> (see also the <a href="https://github.com/mdn/learning-area/blob/master/html/forms/form-validation/fruit-required.html">source code</a>.)</p>
</div>

<p>Try submitting the form without a value. Note how the invalid input gets focus, a default error message ("Please fill out this field") appears, and the form is prevented from being sent.</p>

<p>The presence of the <code>required</code> attribute on any element that supports this attribute means the element matches the {{cssxref(':required')}} pseudoclass whether it has a value or not. If the {{HTMLElement("input")}} has no value, the <code>input</code> will match the {{cssxref(':invalid')}} pseudoclass.</p>

<div class="notecard note">
<p><strong>Note:</strong> For good user experience, indicate to the user when form fields are required. It isn't only good user experience, it is required by WCAG <a href="/en-US/docs/Learn/Accessibility">accessibility</a> guidelines. Also, only require users to input data you actually need: For example, why do you really need to know someone's gender or title?</p>
</div>

<h3 id="Validating_against_a_regular_expression">Validating against a regular expression</h3>

<p>Another useful validation feature is the <a href="/en-US/docs/Web/HTML/Attributes/pattern"><code>pattern</code></a> attribute, which expects a <a href="/en-US/docs/Web/JavaScript/Guide/Regular_Expressions">Regular Expression</a> as its value. A regular expression (regex) is a pattern that can be used to match character combinations in text strings, so regexps are ideal for form validation and serve a variety of other uses in JavaScript.</p>

<p>Regexps are quite complex, and we don't intend to teach you them exhaustively in this article. Below are some examples to give you a basic idea of how they work.</p>

<ul>
 <li><code>a</code> — Matches one character that is <code>a</code> (not <code>b</code>, not <code>aa</code>, and so on).</li>
 <li><code>abc</code> — Matches <code>a</code>, followed by <code>b</code>, followed by <code>c</code>.</li>
 <li><code>ab?c</code>—Matches <code>a</code>, optionally followed by a single <code>b</code>, followed by <code>c</code>. ( <code>ac</code> or <code>abc</code>)</li>
 <li><code>ab*c</code>—Matches <code>a</code>, optionally followed by any number of <code>b</code>s, followed by <code>c</code>. ( <code>ac</code> , <code>abc</code>, <code>abbbbbc</code>, and so on).</li>
 <li><code>a|b</code> — Matches one character that is <code>a</code> or <code>b</code>.</li>
 <li><code>abc|xyz</code> — Matches exactly <code>abc</code> or exactly <code>xyz</code> (but not <code>abcxyz</code> or <code>a</code> or <code>y</code>, and so on).</li>
</ul>

<p>There are many more possibilities that we don't cover here. For a complete list and many examples, consult our <a href="/en-US/docs/Web/JavaScript/Guide/Regular_Expressions">Regular expressions</a> documentation.</p>

<p>Let's implement an example. Update your HTML to add a <a href="/en-US/docs/Web/HTML/Attributes/pattern"><code>pattern</code></a> attribute like this:</p>

<pre class="brush: html">&lt;form&gt;
  &lt;label for="choose"&gt;Would you prefer a banana or a cherry?&lt;/label&gt;
  &lt;input id="choose" name="i_like" required pattern="[Bb]anana|[Cc]herry"&gt;
  &lt;button&gt;Submit&lt;/button&gt;
&lt;/form&gt;
</pre>

<pre class="brush: css hidden">input:invalid {
  border: 2px dashed red;
}

input:valid {
  border: 2px solid black;
}</pre>

<p>This gives us the following update — try it out:</p>

<p>{{EmbedLiveSample("Validating_against_a_regular_expression", "100%", 80)}}</p>

<div class="notecard note">
<p><strong>Note:</strong> You can find this example live on GitHub as <a href="https://mdn.github.io/learning-area/html/forms/form-validation/fruit-pattern.html">fruit-pattern.html</a> (see also the <a href="https://github.com/mdn/learning-area/blob/master/html/forms/form-validation/fruit-pattern.html">source code</a>.)</p>
</div>

<p>In this example, the {{HTMLElement("input")}} element accepts one of four possible values: the strings "banana", "Banana", "cherry", or "Cherry". Regular expressions are case-sensitive, but we've made it support capitalized as well as lower-case versions using an extra "Aa" pattern nested inside square brackets.</p>

<p>At this point, try changing the value inside the <a href="/en-US/docs/Web/HTML/Attributes/pattern"><code>pattern</code></a> attribute to equal some of the examples you saw earlier, and look at how that affects the values you can enter to make the input value valid. Try writing some of your own, and see how it goes. Make them fruit-related where possible so that your examples make sense!</p>

<p>If a non-empty value of the {{HTMLElement("input")}} doesn't match the regular expression's pattern, the <code>input</code> will match the {{cssxref(':invalid')}} pseudoclass.</p>

<div class="notecard note">
<p><strong>Note:</strong> Some {{HTMLElement("input")}} element types don't need a <a href="/en-US/docs/Web/HTML/Attributes/pattern"><code>pattern</code></a> attribute to be validated against a regular expression. Specifying the <code>email</code> type, for example, validates the inputs value against a well-formed email address pattern or a pattern matching a comma-separated list of email addresses if it has the <a href="/en-US/docs/Web/HTML/Attributes/multiple"><code>multiple</code></a> attribute.</p>
</div>

<div class="notecard note">
<p><strong>Note:</strong> The {{HTMLElement("textarea")}} element doesn't support the <a href="/en-US/docs/Web/HTML/Attributes/pattern"><code>pattern</code></a> attribute.</p>
</div>

<h3 id="Constraining_the_length_of_your_entries">Constraining the length of your entries</h3>

<p>You can constrain the character length of all text fields created by {{HTMLElement("input")}} or {{HTMLElement("textarea")}} by using the <a href="/en-US/docs/Web/HTML/Attributes/minlength"><code>minlength</code></a> and <code><a href="/en-US/docs/Web/HTML/Attributes/maxlength">maxlength</a></code> attributes. A field is invalid if it has a value and that value has fewer characters than the <a href="/en-US/docs/Web/HTML/Attributes/minlength"><code>minlength</code></a> value or more than the <code><a href="/en-US/docs/Web/HTML/Attributes/maxlength">maxlength</a></code> value.</p>

<p>Browsers often don't let the user type a longer value than expected into text fields. A better user experience than just using <code>maxlength</code> is to also provide character count feedback in an accessible manner and let them edit their content down to size. An example of this is the character limit seen on Twitter when Tweeting. JavaScript, including <a href="https://github.com/mimo84/bootstrap-maxlength">solutions using <code>maxlength</code></a>, can be used to provide this</p>

<h3 id="Constraining_the_values_of_your_entries">Constraining the values of your entries</h3>

<p>For number fields (i.e. <code><a href="/en-US/docs/Web/HTML/Element/input/number">&lt;input type="number"&gt;</a></code>), the <code><a href="/en-US/docs/Web/HTML/Attributes/min">min</a></code> and <code><a href="/en-US/docs/Web/HTML/Attributes/max">max</a></code> attributes can be used to provide a range of valid values. If the field contains a value outside this range, it will be invalid.</p>

<p>Let's look at another example. Create a new copy of the <a href="https://github.com/mdn/learning-area/blob/master/html/forms/form-validation/fruit-start.html">fruit-start.html</a> file.</p>

<p>Now delete the contents of the <code>&lt;body&gt;</code> element, and replace it with the following:</p>

<pre class="brush: html">&lt;form&gt;
  &lt;div&gt;
    &lt;label for="choose"&gt;Would you prefer a banana or a cherry?&lt;/label&gt;
    &lt;input type="text" id="choose" name="i_like" required minlength="6" maxlength="6"&gt;
  &lt;/div&gt;
  &lt;div&gt;
    &lt;label for="number"&gt;How many would you like?&lt;/label&gt;
    &lt;input type="number" id="number" name="amount" value="1" min="1" max="10"&gt;
  &lt;/div&gt;
  &lt;div&gt;
    &lt;button&gt;Submit&lt;/button&gt;
  &lt;/div&gt;
&lt;/form&gt;</pre>

<ul>
 <li>Here you'll see that we've given the <code>text</code> field a <code>minlength</code> and <code>maxlength</code> of six, which is the same length as banana and cherry.</li>
 <li>We've also given the <code>number</code> field a <code>min</code> of one and a <code>max</code> of ten. Entered numbers outside this range will show as invalid; users won't be able to use the increment/decrement arrows to move the value outside of this range. If the user manually enters a number outside of this range, the data is invalid. The number is not required, so removing the value will still result in a valid value.</li>
</ul>

<pre class="brush: css hidden">input:invalid {
  border: 2px dashed red;
}

input:valid {
  border: 2px solid black;
}

div {
  margin-bottom: 10px;
}</pre>

<p>Here is the example running live:</p>

<p>{{EmbedLiveSample("Constraining_the_values_of_your_entries", "100%", 100)}}</p>

<div class="notecard note">
<p><strong>Note:</strong> You can find this example live on GitHub as <a href="https://mdn.github.io/learning-area/html/forms/form-validation/fruit-length.html">fruit-length.html</a> (see also the <a href="https://github.com/mdn/learning-area/blob/master/html/forms/form-validation/fruit-length.html">source code</a>.)</p>
</div>

<div class="notecard note">
<p><strong>Note:</strong> <code>&lt;input type="number"&gt;</code> (and other types, such as <code>range</code> and <code>date</code>) can also take a <a href="/en-US/docs/Web/HTML/Attributes/step"><code>step</code></a> attribute, which specifies what increment the value will go up or down by when the input controls are used (such as the up and down number buttons). In the above example we've not included a <code>step</code> attribute, so the value defaults to <code>1</code>. This means that floats, like 3.2, will also show as invalid.</p>
</div>

<h3 id="Full_example">Full example</h3>

<p>Here is a full example to show usage of HTML's built-in validation features. First, some HTML:</p>

<pre class="brush: html">&lt;form&gt;
  &lt;p&gt;
    &lt;fieldset&gt;
      &lt;legend&gt;Do you have a driver's license?&lt;abbr title="This field is mandatory" aria-label="required"&gt;*&lt;/abbr&gt;&lt;/legend&gt;
      &lt;!-- While only one radio button in a same-named group can be selected at a time,
           and therefore only one radio button in a same-named group having the "required"
           attribute suffices in making a selection a requirement --&gt;
      &lt;input type="radio" required name="driver" id="r1" value="yes"&gt;&lt;label for="r1"&gt;Yes&lt;/label&gt;
      &lt;input type="radio" required name="driver" id="r2" value="no"&gt;&lt;label for="r2"&gt;No&lt;/label&gt;
    &lt;/fieldset&gt;
  &lt;/p&gt;
  &lt;p&gt;
    &lt;label for="n1"&gt;How old are you?&lt;/label&gt;
    &lt;!-- The pattern attribute can act as a fallback for browsers which
         don't implement the number input type but support the pattern attribute.
         Please note that browsers that support the pattern attribute will make it
         fail silently when used with a number field.
         Its usage here acts only as a fallback --&gt;
    &lt;input type="number" min="12" max="120" step="1" id="n1" name="age"
           pattern="\d+"&gt;
  &lt;/p&gt;
  &lt;p&gt;
    &lt;label for="t1"&gt;What's your favorite fruit?&lt;abbr title="This field is mandatory" aria-label="required"&gt;*&lt;/abbr&gt;&lt;/label&gt;
    &lt;input type="text" id="t1" name="fruit" list="l1" required
           pattern="[Bb]anana|[Cc]herry|[Aa]pple|[Ss]trawberry|[Ll]emon|[Oo]range"&gt;
    &lt;datalist id="l1"&gt;
      &lt;option&gt;Banana&lt;/option&gt;
      &lt;option&gt;Cherry&lt;/option&gt;
      &lt;option&gt;Apple&lt;/option&gt;
      &lt;option&gt;Strawberry&lt;/option&gt;
      &lt;option&gt;Lemon&lt;/option&gt;
      &lt;option&gt;Orange&lt;/option&gt;
    &lt;/datalist&gt;
  &lt;/p&gt;
  &lt;p&gt;
    &lt;label for="t2"&gt;What's your e-mail address?&lt;/label&gt;
    &lt;input type="email" id="t2" name="email"&gt;
  &lt;/p&gt;
  &lt;p&gt;
    &lt;label for="t3"&gt;Leave a short message&lt;/label&gt;
    &lt;textarea id="t3" name="msg" maxlength="140" rows="5"&gt;&lt;/textarea&gt;
  &lt;/p&gt;
  &lt;p&gt;
    &lt;button&gt;Submit&lt;/button&gt;
  &lt;/p&gt;
&lt;/form&gt;</pre>

<p>And now some CSS to style the HTML:</p>

<pre class="brush: css">form {
  font: 1em sans-serif;
  max-width: 320px;
}

p &gt; label {
  display: block;
}

input[type="text"],
input[type="email"],
input[type="number"],
textarea,
fieldset {
  width : 100%;
  border: 1px solid #333;
  box-sizing: border-box;
}

input:invalid {
  box-shadow: 0 0 5px 1px red;
}

input:focus:invalid {
  box-shadow: none;
}</pre>

<p>This renders as follows:</p>

<p>{{EmbedLiveSample("Full_example", "100%", 420)}}</p>

<p>See <a href="/en-US/docs/Web/Guide/HTML/HTML5/Constraint_validation#validation-related_attributes">Validation-related attributes</a> for a complete list of attributes that can be used to constrain input values and the input types that support them.</p>

<div class="notecard note">
<p><strong>Note:</strong> You can find this example live on GitHub as <a href="https://mdn.github.io/learning-area/html/forms/form-validation/full-example.html">full-example.html</a> (see also the <a href="https://github.com/mdn/learning-area/blob/master/html/forms/form-validation/full-example.html">source code</a>.)</p>
</div>

<h2 id="Validating_forms_using_JavaScript">Validating forms using JavaScript</h2>

<p>You must use JavaScript if you want to take control over the look and feel of native error messages or to deal with legacy browsers that do not support HTML's built-in form validation. In this section we will look at the different ways to do this.</p>

<h3 id="The_Constraint_Validation_API">The Constraint Validation API</h3>

<p>Most browsers support the <a href="/en-US/docs/Web/API/Constraint_validation">Constraint Validation API</a>, which consists of a set of methods and properties available on the following form element DOM interfaces:</p>

<ul>
 <li><code><a href="/en-US/docs/Web/API/HTMLButtonElement">HTMLButtonElement</a></code> (represents a <code><a href="/en-US/docs/Web/HTML/Element/button">&lt;button&gt;</a></code> element)</li>
 <li><code><a href="/en-US/docs/Web/API/HTMLFieldSetElement">HTMLFieldSetElement</a></code> (represents a <code><a href="/en-US/docs/Web/HTML/Element/fieldset">&lt;fieldset&gt;</a></code> element)</li>
 <li><code><a href="/en-US/docs/Web/API/HTMLInputElement">HTMLInputElement</a></code> (represents an <code><a href="/en-US/docs/Web/HTML/Element/input">&lt;input&gt;</a></code> element)</li>
 <li><code><a href="/en-US/docs/Web/API/HTMLOutputElement">HTMLOutputElement</a></code> (represents an <code><a href="/en-US/docs/Web/HTML/Element/output">&lt;output&gt;</a></code> element)</li>
 <li><code><a href="/en-US/docs/Web/API/HTMLSelectElement">HTMLSelectElement</a></code> (represents a <code><a href="/en-US/docs/Web/HTML/Element/select">&lt;select&gt;</a></code> element)</li>
 <li><code><a href="/en-US/docs/Web/API/HTMLTextAreaElement">HTMLTextAreaElement</a></code> (represents a <code><a href="/en-US/docs/Web/HTML/Element/textarea">&lt;textarea&gt;</a></code> element)</li>
</ul>

<p>The Constraint validation API makes the following properties available on the above elements.</p>

<ul>
 <li><code>validationMessage</code>: Returns a localized message describing the validation constraints that the control doesn't satisfy (if any). If the control is not a candidate for constraint validation (<code>willValidate</code> is <code>false</code>) or the element's value satisfies its constraints (is valid), this will return an empty string.</li>
 <li><code>validity</code>: Returns a <code>ValidityState</code> object that contains several properties describing the validity state of the element. You can find full details of all the available properties in the {{domxref("ValidityState")}} reference page; below is listed a few of the more common ones:
  <ul>
   <li>{{domxref("ValidityState.patternMismatch", "patternMismatch")}}: Returns <code>true</code> if the value does not match the specified {{htmlattrxref("pattern", "input")}}, and <code>false</code> if it does match. If true, the element matches the {{cssxref(":invalid")}} CSS pseudo-class.</li>
   <li>{{domxref("ValidityState.tooLong", "tooLong")}}: Returns <code>true</code> if the value is longer than the maximum length specified by the {{htmlattrxref("maxlength", "input")}} attribute, or <code>false</code> if it is shorter than or equal to the maximum. If true, the element matches the {{cssxref(":invalid")}} CSS pseudo-class.</li>
   <li>{{domxref("ValidityState.tooShort", "tooShort")}}: Returns <code>true</code> if the value is shorter than the minimum length specified by the {{htmlattrxref("minlength", "input")}} attribute, or <code>false</code> if it is greater than or equal to the minimum. If true, the element matches the {{cssxref(":invalid")}} CSS pseudo-class.</li>
   <li>{{domxref("ValidityState.rangeOverflow", "rangeOverflow")}}: Returns <code>true</code> if the value is greater than the maximum specified by the {{htmlattrxref("max", "input")}} attribute, or <code>false</code> if it is less than or equal to the maximum. If true, the element matches the {{cssxref(":invalid")}} and {{cssxref(":out-of-range")}} CSS pseudo-classes.</li>
   <li>{{domxref("ValidityState.rangeUnderflow", "rangeUnderflow")}}: Returns <code>true</code> if the value is less than the minimum specified by the {{htmlattrxref("min", "input")}} attribute, or <code>false</code> if it is greater than or equal to the minimum. If true, the element matches the {{cssxref(":invalid")}} and {{cssxref(":out-of-range")}} CSS pseudo-classes.</li>
   <li>{{domxref("ValidityState.typeMismatch", "typeMismatch")}}: Returns <code>true</code> if the value is not in the required syntax (when {{htmlattrxref("type", "input")}} is <code>email</code> or <code>url</code>), or <code>false</code> if the syntax is correct. If <code>true</code>, the element matches the {{cssxref(":invalid")}} CSS pseudo-class.</li>
   <li><code>valid</code>: Returns <code>true</code> if the element meets all its validation constraints, and is therefore considered to be valid, or <code>false</code> if it fails any constraint. If true, the element matches the {{cssxref(":valid")}} CSS pseudo-class; the {{cssxref(":invalid")}} CSS pseudo-class otherwise.</li>
   <li><code>valueMissing</code>: Returns <code>true</code> if the element has a {{htmlattrxref("required", "input")}} attribute, but no value, or <code>false</code> otherwise. If true, the element matches the {{cssxref(":invalid")}} CSS pseudo-class.</li>
  </ul>
 </li>
 <li><code>willValidate</code>: Returns <code>true</code> if the element will be validated when the form is submitted; <code>false</code> otherwise.</li>
</ul>

<p>The Constraint Validation API also makes the following methods available on the above elements and the <code><a href="/en-US/docs/Web/HTML/Element/form">form</a></code> element.</p>

<ul>
 <li><code>checkValidity()</code>: Returns <code>true</code> if the element's value has no validity problems; <code>false</code> otherwise. If the element is invalid, this method also fires an <a href="/en-US/docs/Web/API/HTMLInputElement/invalid_event"><code>invalid</code> event</a> on the element.</li>
 <li><code>reportValidity()</code>: Reports invalid field(s) using events. Useful in combination with <code>preventDefault()</code> in an <code>onSubmit</code> event handler</li>
 <li><code>setCustomValidity(<em>message</em>)</code>: Adds a custom error message to the element; if you set a custom error message, the element is considered to be invalid, and the specified error is displayed. This lets you use JavaScript code to establish a validation failure other than those offered by the standard HTML5 validation constraints. The message is shown to the user when reporting the problem.</li>
</ul>

<h4 id="Implementing_a_customized_error_message">Implementing a customized error message</h4>

<p>As you saw in the HTML5 validation constraint examples earlier, each time a user tries to submit an invalid form, the browser displays an error message. The way this message is displayed depends on the browser.</p>

<p>These automated messages have two drawbacks:</p>

<ul>
 <li>There is no standard way to change their look and feel with CSS.</li>
 <li>They depend on the browser locale, which means that you can have a page in one language but an error message displayed in another language, as seen in the following Firefox screenshot.</li>
</ul>

<p><img alt="Example of an error message with Firefox in French on an English page" src="error-firefox-win7.png"></p>

<p>Customizing these error messages is one of the most common use cases of the <a href="/en-US/docs/Web/API/Constraint_validation" rel="external">constraint validation API</a>. Let's work through a simple example of how to do this.</p>

<p>We'll start with some simple HTML (feel free to put this in a blank HTML file; use a fresh copy of <a href="https://github.com/mdn/learning-area/blob/master/html/forms/form-validation/fruit-start.html">fruit-start.html</a> as a basis, if you like):</p>

<pre class="brush: html">&lt;form&gt;
  &lt;label for="mail"&gt;I would like you to provide me with an e-mail address:&lt;/label&gt;
  &lt;input type="email" id="mail" name="mail"&gt;
  &lt;button&gt;Submit&lt;/button&gt;
&lt;/form&gt;</pre>

<p>And add the following JavaScript to the page:</p>

<pre class="brush: js">const email = document.getElementById("mail");

email.addEventListener("input", function (event) {
  if (email.validity.typeMismatch) {
    email.setCustomValidity("I am expecting an e-mail address!");
  } else {
    email.setCustomValidity("");
  }
});</pre>

<p>Here we store a reference to the email input, then add an event listener to it that runs the contained code each time the value inside the input is changed.</p>

<p>Inside the contained code, we check whether the email input's <code>validity.typeMismatch</code> property returns <code>true</code>, meaning that the contained value doesn't match the pattern for a well-formed email address. If so, we call the {{domxref("HTMLInputElement.setCustomValidity()","setCustomValidity()")}} method with a custom message. This renders the input invalid, so that when you try to submit the form, submission fails and the custom error message is displayed.</p>

<p>If the <code>validity.typeMismatch</code> property returns <code>false</code>, we call the <code>setCustomValidity()</code> method an empty string. This renders the input valid, so the form will submit.</p>

<p>You can try it out below:</p>

<p>{{EmbedGHLiveSample("learning-area/html/forms/form-validation/custom-error-message.html", '100%', 80)}}</p>

<div class="notecard note">
<p><strong>Note:</strong> You can find this example live on GitHub as <a href="https://mdn.github.io/learning-area/html/forms/form-validation/custom-error-message.html">custom-error-message.html</a> (see also the <a href="https://github.com/mdn/learning-area/blob/master/html/forms/form-validation/custom-error-message.html">source code</a>.)</p>
</div>

<h4 id="A_more_detailed_example">A more detailed example</h4>

<p>Now that we've seen a really simple example, let's see how we can use this API to build some slightly more complex custom validation.</p>

<p>First, the HTML. Again, feel free to build this along with us:</p>

<pre class="brush: html">&lt;form novalidate&gt;
  &lt;p&gt;
    &lt;label for="mail"&gt;
      &lt;span&gt;Please enter an email address:&lt;/span&gt;
      &lt;input type="email" id="mail" name="mail" required minlength="8"&gt;
      &lt;span class="error" aria-live="polite"&gt;&lt;/span&gt;
    &lt;/label&gt;
  &lt;/p&gt;
  &lt;button&gt;Submit&lt;/button&gt;
&lt;/form&gt;</pre>

<p>This simple form uses the <code><a href="/en-US/docs/Web/HTML/Attributes/novalidate">novalidate</a></code> attribute to turn off the browser's automatic validation; this lets our script take control over validation. However, this doesn't disable support for the constraint validation API nor the application of CSS pseudo-classes like {{cssxref(":valid")}}, etc. That means that even though the browser doesn't automatically check the validity of the form before sending its data, you can still do it yourself and style the form accordingly.</p>

<p>Our input to validate is an <code><a href="/en-US/docs/Web/HTML/Element/input/email">&lt;input type="email"&gt;</a></code>, which is <code>required</code>, and has a <code>minlength</code> of 8 characters. Let's check these using our own code, and show a custom error message for each one.</p>

<p>We are aiming to show the error messages inside a <code>&lt;span&gt;</code> element. The <a href="/en-US/docs/Web/Accessibility/ARIA/ARIA_Live_Regions"><code>aria-live</code></a> attribute is set on that <code>&lt;span&gt;</code> to make sure that our custom error message will be presented to everyone, including it being read out to screenreader users.</p>

<div class="notecard note">
<p><strong>Note:</strong> A key point here is that setting the <code>novalidate</code> attribute on the form is what stops the form from showing its own error message bubbles, and allows us to instead display the custom error messages in the DOM in some manner of our own choosing.</p>
</div>

<p>Now onto some basic CSS to improve the look of the form slightly, and provide some visual feedback when the input data is invalid:</p>

<pre class="brush: css">body {
  font: 1em sans-serif;
  width: 200px;
  padding: 0;
  margin : 0 auto;
}

p * {
  display: block;
}

input[type=email]{
  -webkit-appearance: none;
  appearance: none;

  width: 100%;
  border: 1px solid #333;
  margin: 0;

  font-family: inherit;
  font-size: 90%;

  box-sizing: border-box;
}

/* This is our style for the invalid fields */
input:invalid{
  border-color: #900;
  background-color: #FDD;
}

input:focus:invalid {
  outline: none;
}

/* This is the style of our error messages */
.error {
  width  : 100%;
  padding: 0;

  font-size: 80%;
  color: white;
  background-color: #900;
  border-radius: 0 0 5px 5px;

  box-sizing: border-box;
}

.error.active {
  padding: 0.3em;
}</pre>

<p>Now lets look at the JavaScript that implements the custom error validation.</p>

<pre class="brush: js">// There are many ways to pick a DOM node; here we get the form itself and the email
// input box, as well as the span element into which we will place the error message.
const form  = document.getElementsByTagName('form')[0];

const email = document.getElementById('mail');
const emailError = document.querySelector('#mail + span.error');

email.addEventListener('input', function (event) {
  // Each time the user types something, we check if the
  // form fields are valid.

  if (email.validity.valid) {
    // In case there is an error message visible, if the field
    // is valid, we remove the error message.
    emailError.textContent = ''; // Reset the content of the message
    emailError.className = 'error'; // Reset the visual state of the message
  } else {
    // If there is still an error, show the correct error
    showError();
  }
});

form.addEventListener('submit', function (event) {
  // if the email field is valid, we let the form submit

  if(!email.validity.valid) {
    // If it isn't, we display an appropriate error message
    showError();
    // Then we prevent the form from being sent by canceling the event
    event.preventDefault();
  }
});

function showError() {
  if(email.validity.valueMissing) {
    // If the field is empty,
    // display the following error message.
    emailError.textContent = 'You need to enter an e-mail address.';
  } else if(email.validity.typeMismatch) {
    // If the field doesn't contain an email address,
    // display the following error message.
    emailError.textContent = 'Entered value needs to be an e-mail address.';
  } else if(email.validity.tooShort) {
    // If the data is too short,
    // display the following error message.
    emailError.textContent = `Email should be at least ${ email.minLength } characters; you entered ${ email.value.length }.`;
  }

  // Set the styling appropriately
  emailError.className = 'error active';
}</pre>

<p>The comments explain things pretty well, but briefly:</p>

<ul>
 <li>Every time we change the value of the input, we check to see if it contains valid data. If it has then we remove any error message being shown. If the data is not valid, we run <code>showError()</code> to show the appropriate error.</li>
 <li>Every time we try to submit the form, we again check to see if the data is valid. If so, we let the form submit. If not, we run <code>showError()</code> to show the appropriate error, and stop the form submitting with <code><a href="/en-US/docs/Web/API/Event/preventDefault">preventDefault()</a></code>.</li>
 <li>The <code>showError()</code> function uses various properties of the input's <code>validity</code> object to determine what the error is, and then displays an error message as appropriate.</li>
</ul>

<p>Here is the live result:</p>

<p>{{EmbedGHLiveSample("learning-area/html/forms/form-validation/detailed-custom-validation.html", '100%', 150)}}</p>

<div class="notecard note">
<p><strong>Note:</strong> You can find this example live on GitHub as <a href="https://mdn.github.io/learning-area/html/forms/form-validation/detailed-custom-validation.html">detailed-custom-validation.html</a> (see also the <a href="https://github.com/mdn/learning-area/blob/master/html/forms/form-validation/detailed-custom-validation.html">source code</a>.)</p>
</div>

<p>The constraint validation API gives you a powerful tool to handle form validation, letting you have enormous control over the user interface above and beyond what you can do with HTML and CSS alone.</p>

<div class="notecard note">
<p><strong>Note:</strong> For further information, see our <a href="/en-US/docs/Web/Guide/HTML/HTML5/Constraint_validation">Constraint validation guide</a>, and the <a href="/en-US/docs/Web/API/Constraint_validation">Constraint Validation API</a> reference.</p>
</div>

<h3 id="Validating_forms_without_a_built-in_API">Validating forms without a built-in API</h3>

<p>In some cases, such as legacy browser support or <a href="/en-US/docs/Learn/Forms/How_to_build_custom_form_controls">custom controls</a>, you won't be able to or won't want to use the Constraint Validation API.You're still able to use JavaScript to validate your form, but you'll just have to write your own.</p>

<p>To validate a form, ask yourself a few questions:</p>

<dl>
 <dt>What kind of validation should I perform?</dt>
 <dd>You need to determine how to validate your data: string operations, type conversion, regular expressions, and so on. It's up to you.</dd>
 <dt>What should I do if the form doesn't validate?</dt>
 <dd>This is clearly a UI matter. You have to decide how the form will behave. Does the form send the data anyway? Should you highlight the fields that are in error? Should you display error messages?</dd>
 <dt>How can I help the user to correct invalid data?</dt>
 <dd>In order to reduce the user's frustration, it's very important to provide as much helpful information as possible in order to guide them in correcting their inputs. You should offer up-front suggestions so they know what's expected, as well as clear error messages. If you want to dig into form validation UI requirements, here are some useful articles you should read:
 <ul>
  <li>SmashingMagazine: <a href="https://uxdesign.smashingmagazine.com/2012/06/27/form-field-validation-errors-only-approach/" rel="external">Form-Field Validation: The Errors-Only Approach</a></li>
  <li>SmashingMagazine: <a href="https://www.smashingmagazine.com/2009/07/07/web-form-validation-best-practices-and-tutorials/" rel="external">Web Form Validation: Best Practices and Tutorials</a></li>
  <li>WebFX: <a href="https://www.webfx.com/blog/web-design/10-tips-for-optimizing-web-form-submission-usability/" rel="external">10 Tips for Optimizing Web Form Submission Usability</a></li>
  <li>A List Apart: <a href="https://www.alistapart.com/articles/inline-validation-in-web-forms/" rel="external">Inline Validation in Web Forms</a></li>
 </ul>
 </dd>
</dl>

<h4 id="An_example_that_doesnt_use_the_constraint_validation_API">An example that doesn't use the constraint validation API</h4>

<p>In order to illustrate this, the following is a simplified version of the previous example that works with legacy browsers.</p>

<p>The HTML is almost the same; we just removed the HTML validation features.</p>

<pre class="brush: html">&lt;form&gt;
  &lt;p&gt;
    &lt;label for="mail"&gt;
        &lt;span&gt;Please enter an email address:&lt;/span&gt;
        &lt;input type="text" id="mail" name="mail"&gt;
        &lt;span class="error" aria-live="polite"&gt;&lt;/span&gt;
    &lt;/label&gt;
  &lt;/p&gt;
  &lt;!-- Some legacy browsers need to have the `type` attribute
       explicitly set to `submit` on the `button`element --&gt;
  &lt;button type="submit"&gt;Submit&lt;/button&gt;
&lt;/form&gt;</pre>

<p>Similarly, the CSS doesn't need to change very much; we've just turned the {{cssxref(":invalid")}} CSS pseudo-class into a real class and avoided using the attribute selector that doesn't work on Internet Explorer 6.</p>

<pre class="brush: css">body {
  font: 1em sans-serif;
  width: 200px;
  padding: 0;
  margin : 0 auto;
}

form {
  max-width: 200px;
}

p * {
  display: block;
}

input.mail {
  -webkit-appearance: none;

  width: 100%;
  border: 1px solid #333;
  margin: 0;

  font-family: inherit;
  font-size: 90%;

  box-sizing: border-box;
}

/* This is our style for the invalid fields */
input.invalid{
  border-color: #900;
  background-color: #FDD;
}

input:focus.invalid {
  outline: none;
}

/* This is the style of our error messages */
.error {
  width  : 100%;
  padding: 0;

  font-size: 80%;
  color: white;
  background-color: #900;
  border-radius: 0 0 5px 5px;
  box-sizing: border-box;
}

.error.active {
  padding: 0.3em;
}</pre>

<p>The big changes are in the JavaScript code, which needs to do much more heavy lifting.</p>

<pre class="brush: js">// There are fewer ways to pick a DOM node with legacy browsers
const form  = document.getElementsByTagName('form')[0];
const email = document.getElementById('mail');

// The following is a trick to reach the next sibling Element node in the DOM
// This is dangerous because you can easily build an infinite loop.
// In modern browsers, you should prefer using element.nextElementSibling
let error = email;
while ((error = error.nextSibling).nodeType != 1);

// As per the HTML5 Specification
const emailRegExp = /^[a-zA-Z0-9.!#$%&amp;'*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$/;

// Many legacy browsers do not support the addEventListener method.
// Here is a simple way to handle this; it's far from the only one.
function addEvent(element, event, callback) {
  let previousEventCallBack = element["on"+event];
  element["on"+event] = function (e) {
    const output = callback(e);

    // A callback that returns `false` stops the callback chain
    // and interrupts the execution of the event callback.
    if (output === false) return false;

    if (typeof previousEventCallBack === 'function') {
      output = previousEventCallBack(e);
      if(output === false) return false;
    }
  }
};

// Now we can rebuild our validation constraint
// Because we do not rely on CSS pseudo-class, we have to
// explicitly set the valid/invalid class on our email field
addEvent(window, "load", function () {
  // Here, we test if the field is empty (remember, the field is not required)
  // If it is not, we check if its content is a well-formed e-mail address.
  const test = email.value.length === 0 || emailRegExp.test(email.value);

  email.className = test ? "valid" : "invalid";
});

// This defines what happens when the user types in the field
addEvent(email, "input", function () {
  const test = email.value.length === 0 || emailRegExp.test(email.value);
  if (test) {
    email.className = "valid";
    error.textContent = "";
    error.className = "error";
  } else {
    email.className = "invalid";
  }
});

// This defines what happens when the user tries to submit the data
addEvent(form, "submit", function () {
  const test = email.value.length === 0 || emailRegExp.test(email.value);

  if (!test) {
    email.className = "invalid";
    error.textContent = "I expect an e-mail, darling!";
    error.className = "error active";

    // Some legacy browsers do not support the event.preventDefault() method
    return false;
  } else {
    email.className = "valid";
    error.textContent = "";
    error.className = "error";
  }
});</pre>

<p>The result looks like this:</p>

<p>{{EmbedLiveSample("An_example_that_doesnt_use_the_constraint_validation_API", "100%", 130)}}</p>

<p>As you can see, it's not that hard to build a validation system on your own. The difficult part is to make it generic enough to use both cross-platform and on any form you might create. There are many libraries available to perform form validation, such as <a href="https://rickharrison.github.io/validate.js/" rel="external">Validate.js</a>.</p>

<h2 id="Test_your_skills!">Test your skills!</h2>

<p>You've reached the end of this article, but can you remember the most important information? You can find some further tests to verify that you've retained this information before you move on — see <a href="/en-US/docs/Learn/Forms/Test_your_skills:_Form_validation">Test your skills: Form validation</a>.</p>

<h2 id="Summary">Summary</h2>

<p>Client-side form validation sometimes requires JavaScript if you want to customize styling and error messages, but it <em>always</em> requires you to think carefully about the user. Always remember to help your users correct the data they provide. To that end, be sure to:</p>

<ul>
 <li>Display explicit error messages.</li>
 <li>Be permissive about the input format.</li>
 <li>Point out exactly where the error occurs, especially on large forms.</li>
</ul>

<p>Once you have checked that the form is filled out correctly, the form can be submitted. We'll cover <a href="/en-US/docs/Learn/Forms/Sending_and_retrieving_form_data">sending form data</a> next.</p>

<p>{{PreviousMenuNext("Learn/Forms/UI_pseudo-classes", "Learn/Forms/Sending_and_retrieving_form_data", "Learn/HTML/Forms")}}</p>

<h2 id="In_this_module">In this module</h2>

<ul>
 <li><a href="/en-US/docs/Learn/Forms/Your_first_form">Your first form</a></li>
 <li><a href="/en-US/docs/Learn/Forms/How_to_structure_a_web_form">How to structure a web form</a></li>
 <li><a href="/en-US/docs/Learn/Forms/Basic_native_form_controls">Basic native form controls</a></li>
 <li><a href="/en-US/docs/Learn/Forms/HTML5_input_types">The HTML5 input types</a></li>
 <li><a href="/en-US/docs/Learn/Forms/Other_form_controls">Other form controls</a></li>
 <li><a href="/en-US/docs/Learn/Forms/Styling_web_forms">Styling web forms</a></li>
 <li><a href="/en-US/docs/Learn/Forms/Advanced_form_styling">Advanced form styling</a></li>
 <li><a href="/en-US/docs/Learn/Forms/UI_pseudo-classes">UI pseudo-classes</a></li>
 <li><a href="/en-US/docs/Learn/Forms/Form_validation">Client-side form validation</a></li>
 <li><a href="/en-US/docs/Learn/Forms/Sending_and_retrieving_form_data">Sending form data</a></li>
</ul>

<h3 id="Advanced_Topics">Advanced Topics</h3>

<ul>
 <li><a href="/en-US/docs/Learn/Forms/How_to_build_custom_form_controls">How to build custom form controls</a></li>
 <li><a href="/en-US/docs/Learn/Forms/Sending_forms_through_JavaScript">Sending forms through JavaScript</a></li>
 <li><a href="/en-US/docs/Learn/Forms/Property_compatibility_table_for_form_controls">Property compatibility table for form widgets</a></li>
</ul>