---
layout: post
title: Réparer un DVD sous Ubuntu avec ddrescue
tags: dvd ubuntu ddrescue
comments: true
tracking: true
share: true
---

Exemple de trace:

{% highlight console %}
[50097.605036] sr 0:0:0:0: [sr0] Unhandled sense code
[50097.605046] sr 0:0:0:0: [sr0]  
[50097.605051] Result: hostbyte=DID_OK driverbyte=DRIVER_SENSE
[50097.605057] sr 0:0:0:0: [sr0]  
[50097.605061] Sense Key : Medium Error [current] 
[50097.605070] sr 0:0:0:0: [sr0]  
[50097.605076] Add. Sense: L-EC uncorrectable error
[50097.605083] sr 0:0:0:0: [sr0] CDB: 
[50097.605086] Read(10): 28 00 00 02 e3 60 00 00 02 00
[50097.605107] end_request: I/O error, dev sr0, sector 757120
[50097.605116] Buffer I/O error on device sr0, logical block 189280
[50097.605123] Buffer I/O error on device sr0, logical block 189281
{% endhighlight %}

