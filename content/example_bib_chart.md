---
layout: page
title: Bibliographie et graphes 
menu:
  main:
    weight: 3
bibFile: content/bibliography.json
toc: true
---

Un exemple avec de la bibliographie et des graphes avec chart.js.

<!--more-->



## Bibliographie

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed et arcu non lectus pellentesque dignissim quis in ligula. Vestibulum consectetur, tellus nec rhoncus tincidunt, lectus nunc bibendum tortor, id pulvinar purus est sed diam. Donec sed purus eu libero volutpat cursus non a nibh. Suspendisse scelerisque vehicula interdum. Sed interdum augue ut justo pulvinar luctus at sit amet libero. Phasellus tincidunt mi sed aliquam gravida. Etiam nec vulputate elit. Nulla nulla purus, scelerisque vitae libero nec, blandit fermentum risus. Donec mi augue, porttitor non varius et, volutpat ut magna. Praesent vulputate urna vel mattis blandit. Integer quis dui et ligula dictum convallis quis sed sem. Morbi pellentesque vestibulum ex et efficitur. Nulla erat turpis, dapibus id est iaculis, faucibus pellentesque sapien. Pellentesque lobortis malesuada elit, ut luctus arcu condimentum ac. Etiam dignissim erat id condimentum efficitur. Nulla id ipsum ultrices, pretium mauris eu, semper elit. As shown as in {{<cite "palomar2016">}} or in {{<cite "tyler1987">}}.

Mauris nec tortor ac ligula tincidunt dapibus in sit amet libero. Nullam vitae fringilla massa. Sed ac sem quis nunc tincidunt imperdiet. Fusce maximus, sem sed vehicula hendrerit, arcu urna aliquet odio, a porttitor metus mauris tempor eros. Pellentesque non sapien neque. Vestibulum ac metus vestibulum, volutpat magna sed, dapibus nisi. Mauris ut commodo nunc, sed imperdiet est. Nulla vel orci erat. Nulla vitae elit auctor, cursus lectus interdum, sagittis nisi.

Une citation qui n'a rien à voir avec le texte {{<cite "gordana_thesis">}}.


## Ill-conditioned systems

This is an important problem in all numerical analysis based upon linear algebra. Let us first give a definition:

{{<highlight-block "Definition">}}A system of linear equations is said to be **ill-conditioned** when some small perturbation in the system can produce relatively large changes in the exact solution.
{{</highlight-block>}}

To understand this intuitively, let us visualize the 2D-case:
* An equation is a line
* A system of equations is the intersection of two lines

On the left side is an ill-conditioned sytem since moveving slighty one of the line produce a vastly different solution while on the right it is not so big.

<div style="display: flex">
{{< chart 45 400 true>}}
{
            type: 'scatter',
            data: {
                datasets: [{
                    label: 'Line 1',
                    // Assuming the slope and manually calculating two additional points
                    data: [{x: -1, y: -0.6},  {x: 2, y: 2.6}], // Example points for Line 1
                    showLine: true,
                    borderColor: 'red',
                    fill: false
                }, {
                    label: 'Line 2',
                    // Assuming a different slope and manually calculating two additional points
                    data:  [{x: -1, y: -0.8}, {x: 2, y: 2.8}], // Example points for Line 2
                    showLine: true,
                    borderColor: 'blue',
                    fill: false
                }]
            },
            options: {
              maintainAspectRatio: false,
              scales:{
                y: {
                  title: {
                    display: true,
                    text: 'y'
                  }
                },
                x: {
                  title: {
                    display: true,
                    text: 'x'
                  }
                }
              },
              plugins: {
                title: {
                    display: true,
                    text: 'Ill-conditioned System',
                    font: {
                        size: 18
                    }
                },
              }
            }
}
{{< /chart >}}

{{< chart 45 400 true>}}
{
            type: 'scatter',
            data: {
                datasets: [{
                    label: 'Line 1',
                    // Assuming the slope and manually calculating two additional points
                    data: [{x: -1, y: 5},  {x: 2, y: -10}], // Example points for Line 1
                    showLine: true,
                    borderColor: 'red',
                    fill: false
                }, {
                    label: 'Line 2',
                    // Assuming a different slope and manually calculating two additional points
                    data:  [{x: -1, y: -0.8}, {x: 2, y: 2.8}], // Example points for Line 2
                    showLine: true,
                    borderColor: 'blue',
                    fill: false
                }]
            },
            options: {
              maintainAspectRatio: false,
              scales:{
                y: {
                  title: {
                    display: true,
                    text: 'y'
                  }
                },
                x: {
                  title: {
                    display: true,
                    text: 'x'
                  }
                }
              },
              plugins: {
                title: {
                    display: true,
                    text: 'Ill-conditioned System',
                    font: {
                        size: 18
                    }
                },
              }
            }
}
{{< /chart >}}
</div>




## Pie chart

Exemple de diagramme circulaire:
{{< chart 50 400 >}}
{
  type: 'pie',
  data: {
    labels: ['Red', 'Blue', 'Yellow'],
    datasets: [{
      label: 'My First Dataset',
      data: [300, 50, 100],
      backgroundColor: [
        'rgb(255, 99, 132)',
        'rgb(54, 162, 235)',
        'rgb(255, 205, 86)'
      ],
      hoverOffset: 4
    }]
  },
  options: {
    maintainAspectRatio: false,
    plugins: {
      title: {
        display: true,
        text: 'Custom Chart Title',
        font: {
          size: 18
        }
      }
    }
  }
}
{{< /chart >}}


## Série temporelle

Un exmple de graphe d'une série temporelle:

{{< chart 75 400 true>}}
{
  type: 'line',
  data: {
    labels: ['January', 'February', 'March', 'April', 'May', 'June', 'July'],
    datasets: [{
      label: 'My First Dataset',
      data: [65, 59, 80, 81, 56, 55, 40],
      fill: false,
      borderColor: 'rgb(75, 192, 192)',
      tension: 0.1,
    },
    {
      label: 'My Second Dataset',
      data: [35, 49, 70, 71, 46, 45, 30],
      fill: true,
      borderColor: 'rgb(192, 75, 192)',
      tension: 0.1,
    }]
  },
  options: {
    maintainAspectRatio: false,
    plugins:{
      title: {
        display: true,
        text: 'Série temporelle',
        font: {
          size: 18
        }
      }
    }
  }
}
{{< /chart >}}


## Histograms

Un exemple d'histogramme:

{{< chart 75 400 >}}
{
  type: 'bar',
  data: {
    labels: ['Red', 'Blue', 'Yellow', 'Green', 'Purple', 'Orange'],
    datasets: [{
      label: 'My First Dataset',
      data: [65, 59, 80, 81, 56, 55],
      backgroundColor: [
        'rgb(255, 99, 132)',
        'rgb(54, 162, 235)',
        'rgb(255, 205, 86)',
        'rgb(75, 192, 192)',
        'rgb(153, 102, 255)',
        'rgb(255, 159, 64)'
      ],
      borderColor: [
        'rgb(255, 99, 132)',
        'rgb(54, 162, 235)',
        'rgb(255, 205, 86)',
        'rgb(75, 192, 192)',
        'rgb(153, 102, 255)',
        'rgb(255, 159, 64)'
      ],
      borderWidth: 1
    }]
  },
  options: {
    maintainAspectRatio: false,
    plugins: {
      title: {
        display: true,
        text: 'Bar plot',
        font: {
          size: 18
        }
      }
    },
    scales: {
      y: {
        ticks: {
          beginAtZero: true
        }
      }
    }
  }
}
{{< /chart >}}



## Bibliography

<!-- The bibliography will display works from path/to/bib.json -->
{{< bibliography >}}
