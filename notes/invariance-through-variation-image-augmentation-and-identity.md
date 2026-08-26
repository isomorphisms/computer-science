# Invariance through variation: image augmentation, transformations, and identity

These notes record a simple computational example of the same general stacky idea that appears elsewhere in the deformation notes: do not immediately collapse several representations to one quotient point. Keep the representatives, the transformations between them, and the information about which quantities are intended to remain the same.

## The deliberately trivial image-processing laboratory

Start with one raster image `I` of one underlying painting. Public reproductions may already differ by resolution, crop, color management, compression, or resampling. We can also manufacture controlled variants with ordinary image-processing operations.

For example:

```text
I --crop C--> C(I)
I --downsample D--> D(I)
I --C--> C(I) --D--> D(C(I))
```

A shell-level fixture can therefore use ordinary deterministic image tools such as ImageMagick:

```sh
magick original.jpg -crop WxH+X+Y crop.png
magick original.jpg -resize 50% half.png
magick original.jpg -crop WxH+X+Y -resize 50% crop_then_half.png
```

The important bookkeeping distinction is:

1. **underlying-object identity** — the files may depict the same painting;
2. **representation data** — dimensions, crop rectangle, sampling grid, interpolation kernel, color encoding, compression, orientation, and so on;
3. **arrows** — the concrete transformations connecting representations.

Do not erase (2) and (3) merely because (1) is eventually the invariant of interest.

### Composition matters

The transformations compose in the obvious way, but they need not commute. Crop then downsample can differ from downsample then the corresponding crop:

```text
D ∘ C ≠ C' ∘ D
```

Pixel-grid alignment and interpolation can make the byte results differ even when the geometric regions are intended to correspond. Thus a path through representation space can carry information that disappears if all variants are immediately identified.

Cropping and downsampling also generally lose information, so the raw transformation system is not a group of reversible symmetries. It is naturally a category containing both reversible and irreversible arrows. Reversible equivalences can be distinguished inside it rather than pretending that every transformation has an inverse.

### Overlapping crops and local compatibility

Two overlapping crops give a particularly literal descent-shaped fixture:

```text
A = C_A(I)
B = C_B(I)
```

Compute some local datum on `A` and `B` — color statistics, texture descriptors, model activations, etc. Restrict both descriptions to the common region and ask whether they agree there. The computational pattern is:

- local observations on pieces;
- compatibility on overlaps;
- a question about whether the compatible local observations belong to one coherent global object.

This is intentionally boring. It provides a controlled laboratory before asking the same questions about perceptual or learned representations.

## The apparent reversal in machine-learning augmentation

There is a useful linguistic reversal in ordinary data augmentation:

> To learn that something does not change, the learner is often shown many ways that its representation does change.

Let `x` be a raster of a painting and `g` a transformation such as a rotation. If the desired output is invariant under that transformation, then the intended relation is

```text
f(g x) = f(x).
```

A generic learned function has no reason to satisfy that equation. Augmentation samples different points along a transformation orbit and repeatedly supplies the same higher-level label.

So three notions should remain separate:

- **variation:** `x -> g x` changes the representation;
- **equivariance:** an intermediate representation changes in a prescribed way when `g` changes the input;
- **invariance:** a chosen output forgets that variation, e.g. `f(g x) = f(x)`.

The funny but important point is that **establishing invariance may require explicitly exploring variation**.

## Why not quotient immediately?

Suppose several photographs are all photographs of the same person:

```text
front view
profile view
wearing a coat
wearing different clothes
sunlight
indoor light
older photograph
newer photograph
```

If the only task is identity classification, it is tempting to quotient all of these to one label. But that quotient discards potentially useful information about how the observations are related.

Instead retain:

- each concrete observation;
- which person or underlying object it is believed to represent;
- the kind of transformation or change separating observations;
- any witness or provenance for that relationship.

The arrows are heterogeneous. A head turn, lighting change, clothing change, crop, camera movement, perspective projection, JPEG recompression, and aging are not one operation. Yet some higher-level notion of identity may survive all or some of them.

This is closer to a groupoid/action-groupoid picture when the relevant changes are reversible symmetries. Once the observation process includes information-losing maps, partial observations, overlapping local views, or families of compatible descriptions, the broader category/descent/stack language becomes more appropriate.

The key idea is not to invoke the word *stack* merely because several images share a label. The stacky content is in retaining the structured family of representatives and the relations among them instead of replacing the family immediately by a bare equivalence class.

## Rotating a bitmap versus rotating a head

These are importantly different.

A 2-D augmentation such as rotating the finished bitmap acts directly on the observed raster:

```text
image --2D rotation--> rotated image
```

A real change of viewpoint acts before image formation:

```text
person + pose + lighting + camera
              |
              v
          photograph
```

Two photographs from different viewing angles can depict the same person even when no simple 2-D transformation takes one raster to the other. The invariant may therefore live above the image plane, while each raster is a projection of a richer underlying configuration.

That makes the face example structurally richer than ordinary image augmentation: several observation-generating variables can vary while identity is intended to remain fixed.

## Does shear count?

Only relative to a declared invariant.

There is no universal list of transformations under which an object must remain "the same." For example:

- painting identity might reasonably survive some rotation, scaling, cropping, photographic perspective, or color-management changes;
- exact digital-file identity survives none of those;
- geometric-shape identity might or might not survive shear depending on whether the chosen structure is Euclidean, affine, projective, topological, etc.

So the equivalence relation or invariant is itself part of the model. It should be stated rather than silently assumed.

## A useful slogan

```text
Establish invariance by varying the representation.
Do not discard the variation once the equivalence has been established.
```

The first line is ordinary augmentation/equivariance thinking. The second line is the part that points toward the stacky bookkeeping: preserve representatives, transformations, composition, stabilizers/symmetries when present, local restrictions, and provenance instead of retaining only the quotient label.

## Possible executable fixture

A small test corpus could start from one licensed/open image and generate a transformation graph:

```text
original
├── rotate 90°
├── rotate 180°
├── crop A
│   └── downsample 2×
├── crop B
│   └── downsample 2×
└── downsample 2×
    └── corresponding crop
```

For every node, record the exact command and parameters that produced it. For every pair of composable arrows, record the composite. Then a representation model can be tested on both questions separately:

1. what changes along a known arrow?;
2. what chosen quantity remains invariant along that arrow?

That is a much better experimental boundary than simply duplicating many transformed files under one class label and forgetting how they were produced.
