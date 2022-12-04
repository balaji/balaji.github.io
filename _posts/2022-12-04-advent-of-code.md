---
layout: post
title: "Advent of Code"
tags: code
---

[Advent of Code](https://www.adventofcode.com){:target="_blank"} is a code competition website that is publishes new puzzles on the first 25 days of December. One on each day. Each puzzle has two parts. The puzzles start easy and becomes hard progressively. I was introduced to this through my ex-employer (Spotify). I have solved problems in Java, Ruby, Erlang and Scala. I do not have all my problems in github, but I salvaged some of it here: [https://github.com/balaji/advent_of_code](https://github.com/balaji/advent_of_code){:target="_blank"}.

As of writing, I haven't yet managed to solve all 25 puzzles in a year because I traveled to India every single year (except in 2021) during the 4th week of December and I will miss it in the aftermath. In 2021, it was COVID, I was mentally checked out and I was also preparing for interviews for a potential job change (which is an irony because such code competitions are good preparing ground for interviews).

## Stats Viewer
Here is a little fun half a day project. As part of this blog post, I wanted to show my progress on AoC through the years. I used this library: [https://github.com/wimglenn/advent-of-code-data](https://github.com/wimglenn/advent-of-code-data){:target="_blank"}, to set up a AWS Lambda function that fetches only my AoC stats. The code for the AWS Lambda is here: [https://github.com/balaji/adventOfCodeLambda](https://github.com/balaji/adventOfCodeLambda){:target="_blank"}. The charts are done using Chart.js.

My AoC stats are fetched in real-time to produce this graph. Hopefully someday all the bars will be at 25 :)

<div><canvas id="myChart"></canvas></div>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script>
    fetch('https://tovlyfyq7fctucunksa4c7ikcu0mclkd.lambda-url.us-east-1.on.aws/', {
        headers: {
            'Origin': 'https://www.balaji.dev'
        }
    })
    .then((response) => response.json())
    .then((results) => {
        var labels = [];
        var formatted = {};
        var partA = [];
        var partB = [];
        for (const i in results) {
            const result = results[i];
            formatted[result["year"]] = (formatted[result["year"]] || {"a": 0, "b": 0});
            for (const part in result["result"]) {
                formatted[result["year"]][part] += 1;
            }
        }
        for (const i in formatted) {
            partA.push(formatted[i]['a']);
            partB.push(formatted[i]['b']);
            labels.push(i);
        }
        const ctx = document.getElementById('myChart');
        new Chart(ctx, {
            type: 'bar',
            data: {
                labels: labels,
                datasets: [{
                    label: '# of Part A',
                    data: partA,
                    borderWidth: 1
                },
                {
                    label: '# of Part B',
                    data: partB,
                    borderWidth: 1
                }]
            },
            options: {
                scales: {
                    y: {
                        beginAtZero: true,
                        suggestedMax: 25
                    }
                }
            }
        });
    });  
</script>
