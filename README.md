# Colorized

Colorized is a cellular automaton, similar to [Wireworld](https://github.com/GollyGang/ruletablerepository/wiki/WireWorld) or [JvN](http://golly.sourceforge.net/Help/Algorithms/JvN.html). Colorized is notably different from those examples because signals propogate through empty space instead of wires. This isn't a new idea; Colorized is heavily based on Joel Walker's 2011 "Particles" rule (found [here](https://github.com/GollyGang/ruletablerepository/wiki/TheRules#wireworld-and-derivatives)) and Redstoneboi's "Sticky" rule. Rather than aiming for a minimum of states or transitions, the primary goal of Colorized is to be easy to use for computation and construction, with simplicity and minimalism as lower priorities.

You can simulate Colorized using [Golly](http://golly.sourceforge.net/); copy and paste the contents of [`Colorized.rule`](https://raw.githubusercontent.com/HactarCE/Colorized-CA/master/Colorized.rule) into Golly to get started.

## Mechanics

### States

Colorized currently has 15 states:

![All states](/img/states-all.png)

Colorized gets its name from the first eight states, which are the colors black (which doubles as a vacuum/blank state), blue, green, cyan, red, magenta, yellow, and white respectively. These eight states are sometimes referred to as "blocks." Any pattern consisting only of blocks is stable. You may notice that the binary representation of a block corresponds to the red, green, and blue components of its color; this is intentional.

![Color states](/img/states-colors.png)

The next five states are used for photons, which are particles that move at 1 cell/tick (sometimes called the speed of light). The first of these (grey) is used as the tail of a signal, and any of the other four can be a signal head.

![Photon states](/img/states-photons.png)

The last two are temporary states used for construction.

![Construction states](/img/states-construction.png)

### Photons

Colorized has four types of photons:

| Image                                 | Head state | Description         |
|---------------------------------------|------------|---------------------|
| ![Orange photon](/img/photon-9.png)   | 9          | Signal photon       |
| ![Green photon](/img/photon-10.png)   | 10         | Construction photon |
| ![Cyan photon](/img/photon-11.png)    | 11         | Pusher photon       |
| ![Magenta photon](/img/photon-12.png) | 12         | Puller photon       |

(Each photon pictured is moving to the right.)

Photons move orthogonally at [_c_](https://conwaylife.com/wiki/Speed) (1 cell per generation).

### Photon-block interactions

_Copy and paste this RLE into Golly and refer to it while reading this section:_

```
x = 90, y = 10, rule = Colorized
3.G16.HI2.G15.HI2.A35.HI3.G$69.G14.E4.G$60.HI2.E4.G14.2G$69.G$HI2.C
15.HI7.G10.HI7.G30.HI7.G$24.G18.GA38.GE4.G$40.G19.HI7.G13.G.G$64.E4.G
$3.G60.2G$45.G38.G!
```

White is an **absorber**. A head-on collision with white will annihilate the photon.

Cyan is a **splitter**. A head-on collision with cyan will split a photon into two orthogonal ones of the same type.

Blue is a **rotator**. A head-on collision with blue will annihilate the photon, but if a photon slides past blue directly adjacent to it, a new photon of the same type will be produced orthogonal to the original one.

Magenta is a **diagonal duplicator**. In its immediate vicinity, magenta allows photon heads to spread both orthogonally (as normal) and diagonally.

Red, green, and yellow usually behave the same way as white, but it is recommended **not** to use them in pure logic devices due to their special behavior in construction and memory storage.

These interactions are primarily intended for signal photons (orange), but white and cyan work for construction photons (green) as well:

```
x = 30, y = 10, rule = Colorized
3.G16.HJ2.G4$HJ2.C15.HJ7.G$24.G3$3.G!
```

#### Examples

Diode reflector:

```
x = 8, y = 8, rule = Colorized
6.G$HI5.C5$7.C$6.G!
```

Two-way reflector:

```
x = 8, y = 8, rule = Colorized
6.C$HI5.C5$7.C$6.C!
```

Zero-delay orthogonal splitter (using blue):

```
x = 6, y = 3, rule = Colorized
HI$5.A$5.G!
```

Zero-delay orthogonal splitter (using magenta):

```
x = 8, y = 3, rule = Colorized
HI$5.E.G$5.G!
```

Linear diode:

```
x = 8, y = 8, rule = Colorized
.A5.G$2.C2.C$G5.A4$.I5.I$.H5.H!
```

Spiral gun:

```
x = 7, y = 2, rule = Colorized
HI$6.E!
```

Double-photon generator:

```
x = 8, y = 3, rule = Colorized
HI$6.E$6.2G!
```

Two perfect 180-degree reflectors:

```
x = 10, y = 17, rule = Colorized
6.GC$5.C2.G$5.C$.HI5.C$6.A$4.G$7.G6$6.G.G$HI7.C$4.GA$5.C3.G$6.G.C!
```

### Photon-photon interactions

#### Parity

The parity of a photon (similar to a glider's "[color](https://www.conwaylife.com/wiki/Glider#Colour_of_a_glider)" in Conway's Game of Life, although that term would be confusing in this context) is either odd or even, and can be calculated by summing the X and Y positions of the photon along with the generation count. The parity (odd vs. even) of the resulting number gives the parity of the photon. A photon cannot change parity without interacting with a magenta block.

It doesn't really matter whether a given photon has odd or even parity, but it does matter whether two photons have _different_ parities. Two photons are considered "in phase" if they have the same parity, and "out of phase" if their parities differ.

#### Collisions

There are five ways that two photons can collide and interact. The first three are in-phase while the last two are out-of-phase:

![Orange photon collisions](/img/collisions.png)

```
x = 50, y = 12, rule = Colorized
4.G20.G8.2G8.2G5$.HI3.IH3.HI8.HI8.HI4.IH2.HI$25.I$25.H19.I$15.I29.H$
15.H2$4.G19.G9.2G!
```

In the second collision shown above, a cyan or blue block can cause one or both of the photons to survive:

![Symmetric perpendicular collision with survival](/img/collide-2-survive.png)

```
x = 28, y = 8, rule = Colorized
24.G$5.C18.A$HI8.HI8.HI$16.AG8.AG3$5.I9.I9.I$5.H9.H9.H!
```

## Implementation

If you want to construct patterns in Colorized, you do not need to read this section. If you want to understand how Colorized was devised, or to invent a similar automaton, this is the section for you!

### Photon propogation

The tail of a photon will always turn into state 0 (vacuum), and the head of a photon will almost always turn into a tail. A blank cell will turn into any of these photon heads if there is a head orthogonally adjacent to the blank cell AND there is no diagonally adjacent tail that is also orthogonally adjacent to that head.

![Photon propogation](/img/photon-propogation.png)
