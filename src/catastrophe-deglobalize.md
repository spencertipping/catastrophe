Catastrophe caterwaul deglobalization | Spencer Tipping
Licensed under the terms of the MIT source code license

# Introduction

Catastrophe can be used either as a caterwaul module or as a standalone library that contains its own instance of caterwaul. If the latter, then it deglobalizes its caterwaul instance to
create a minimal footprint.

    caterwaul.module('catastrophe.deglobalization', ':all', function ($) {
      $.deglobalize()});