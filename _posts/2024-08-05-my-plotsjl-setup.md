---
layout: post
title: My Plots.jl setup
date: '2024-08-5'
category: guide
---

Remember me saying I wouldn't re-learn another plotting package? Gotcha!

![plots.jl friend](assets/images/plots.jl/new_plots_friend.png){:.ioda}

I didn't bother to learn `Plots.jl` first because I found the documentation a bit lacking. However, once I figured it out I really liked its syntax and ease of configuration. It is also very easy to switch between backends. You can for example use `PGFPlotsX` to generate `tikz`-code that you can put directly into LaTeX!

Here is my `PyPlot` configuration mapped to `Plots.jl`. To use this, add the following code to `$HOME/.julia/config/startup.jl`.

```julia
PLOTS_DEFAULTS = Dict(
        :palette => :Set2,  # Reasonable palette
        :labelfontsize => 12, :tickfontsize => 10, :titlefontsize => 12,
	:fontfamily => "Computer Modern",
        :foreground_color_legend => nothing,  # set the legend frame to invisible
        :framestyle => :box,
        :size => (328, 300),  # size in pixels - width of PLR column width
        :legend => false,  # no legend by default
        :grid => false,
	:msw => 0  # remove outline around markers
)
```