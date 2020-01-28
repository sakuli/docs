+++
title = "Wait mechanism"
date =  2019-12-16T11:50:07+01:00
weight = 7
+++

# Wait
Despite of Sakulis built-in waiting mechanism, it is sometimes neccessary to use customized waiting. An elegant way of doing so is the integrated loop functionality within <a href="https://sakuli.io/apidoc/sakuli-legacy/interfaces/actionapi.html#_wait" target="_blank" rel="noopener"> _wait() </a>. A function given as a second parameter will be executed in a loop as long as in the first parameter of wait defined.  
Use it to wait for an element being accessible by Sakuli and continue your test after.

## Examples of usage

{{<highlight ts>}}
await _wait(5000, () => _isVisible(element));
await _wait(5000, () => _exists(element));
await _wait(20000, () => _assert());
await _wait(5000, () => nativeFind());
{{</highlight>}}

Dont use this mechanism for actions like `_click()`, as it would try to click on an element multiple times within the wait threshold, resulting in an error as soon as the first click succeeded.

