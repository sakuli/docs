+++
title = "Handling Form Fields"
date =  2020-01-17T11:50:07+01:00
weight = 5
+++

# Handling of Form Fields
There are different ways to work with form fields and to manipulate content. Following will show some some examples and best practices.

## Input Mechanisms
You can use multiple mechanisms to fill forms. This comes in handy with non-standard HTML implementations, limitations or bugs within the underlying technology like selenium or webdriver.

### Use of DOM based actions
```await _setValue(element, "value"); ```

### Use of native interactions
The following actions assume your target field is beeing focused. If you need to focus the respective element before, you can use ```await _focus(element);```. Keep in mind that some input fields have an overlay that needs to be clicked before you can actually interact with the underlying field.

#### Coyp & Paste
You can also use the native paste mechanism to paste a string into a field:  
```await env.paste("value");```  

#### Type
With ```env.type("FooBar");``` you can simulate keyboard inputs and thus you are able to write into a textfield like a real user. 
 
## Input Fields of type text / password / etc.

### Textbox
```await _setValue(_textbox("myTextbox", "Value To Enter"));```