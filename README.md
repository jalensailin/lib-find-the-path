# IMPORTANT NOTE!
This is a fork of @dwonderley's module. I am attempting to update it for v10. Just wanted to make sure to credit the original author.

# lib-find-the-path
## Changelog
1.4.0
<p>Added configuration to enable non-omniscient path planning. Added a method to determine line of sight for only vision-blocking walls (i.e. it ignores ethereal walls). Refactored configuration of path manager and utility class.</p>

1.3.0
<p>Refactored segments to provide better structure for eventual hex support.</p>

1.2.0
<ol>
<li>Added FTPUtility class that exposes useful methods
  <ol>
  <li>Added traverse method that moves a token along its calculated path</li>
  <li>Added moveTokenToPoint method</li>
    <ol>
    <li>Moves a token to the location provided by a Point object</li>
    <li>Checks line of sight and collison before movement</li>
    </ol>
  <li>Moved line of sight, collison detection, and isTraversable methods into utility class</li>
  </ol>
<li>Changed collison detection method to take in a configuration object</li>
  <ol>
  <li>The checkCollision option can turn collision checking off</li>
  <li>The token option omits a token from the collision check (prevents a token from colliding with itself)</li>
  <li>In the future, this will allow the user to selectively filter what constitutes a collision</li>
    <ol>
      <li>Can a token move through an allied space or not?</li>
      <li>Can a token stop in an allied space?</li>
      <li>Can a token move through enemy tokens?</li>
    </ol>
  </ol>
<li>Various bug fixes</li>
</ol>

1.1.2
<p>Big, breaking refactor for general improvements, code cleanliness, stability, etc...</p>
<ol>
  <li>Path planning can now be done from macros. See the <a href="https://github.com/dwonderley/lib-find-the-path/wiki">wiki page</a> for more information.</li>
  <li>Added a PoinFactory class that makes creating and using Point objects much easier.</li>
  <li>Added instances of the PathManager and PathFactory classes to game.FindThePath.X.PathManager for general access, where X is the type of distance metric (Chebyshev, Euclidean, or Manhattan).
</ol>

## Summary
<p>This module provides a library that performs system-agnostic path planning calculations for two-dimensional, square grids.</p>

<p>The library may be called "Find the Path," but the Point class is the innovation I think people will find most useful. It provides methods for handling tokens of any size, calculating the minimum distance between points, pixels, and tokens, calculating rotations, and finding neighbors. Importantly, it does these things independent of grid size, distance metrics (it supports several), game system, and the insane coordinate-system-originates-in-the-top-left industry standard. While the PathManager provides methods to find paths between tokens, points, pixels, and coordinates, it's the power of the Point class that actually makes it possible.</p>

<p>See the <a href="https://github.com/dwonderley/lib-find-the-path/wiki">wiki page</a> for examples of this library's use.</p>

<p>This module is a work in progress, but I have tested it extensively. If you find bugs or have questions, comments, or requests, please don't hesitate to leave me a comment here or on the FVTT discord (@Dave Of Wonders).</p>

## Terminology
Token is a FVTT class that contains various information about game tokens, such as their pixel coordinates, the actors they represent, or their names. Tokens exist on the Canvas. The Canvas is a grid of pixel coordinates, originating at the top-left corner, with +x going left to right and +y going top to bottom. 

A pixel coordinate pair (px, py) is the offset from the Canvas origin. A Token has a pixel coordinate pair, a width, and a height. Therefore, a Token's position on the canvas is bounded by the points (px, py), (px + width, py), (px, py + height), (px + width, py + height).

However, because different maps may have different sizes or pixel counts per grid square, it is useful to abstract away the pixel part of the pixel coordinates. A grid coordinate (x, y) is the offset from the Canvas origin in terms of grid squares.

A Point is a wrapper around a pair of grid coordinates. It provides several helpful methods, starting with conversions from Tokens, pixel coordinates, and grid coordinates into a Point object, where calculations can be done more simply.

The PointFactory handles the actual creation of points. It is strongly recommended, as it is more convenient, consistent, and concise than manual construction. The PointFactory needs to be told on construction how distances should be measured between Points. It will only generate points of that type, and it will make sure the size of PointSets (and other data) is managed consistently.

A PointSet is an informal term for an Array of Points. Usually, it is used to represent a Token that has a dimension > 1. It is not a defined class, but it may become one if there is a use for it. These Arrays are provided by methods such as getPointSetFromToken and should not be manually.

A Path is a wrapper around an array of Nodes. Nodes are a private class that do not need to be accessed or constructed, but loosely speaking, they are the path planning equivalent of Points. The path between two Points can be accessed by the Path object's path () method.

The PathManager is a tool used to find and track planned paths. It provides two static methods to compute arbitrary paths, but it's standard usage is to compute paths between Tokens. Using the addToken method, a user can find the path from a Token to a new target Token. This path is stored by the PathManager and accessed by the source and destination token IDs.

Line of Sight (LoS) is the concept that a line drawn from the center of one grid coordinate to the center of another is unobstructed. Collision is the concept of two tokens occupying the same grid space. A Node is said to be traversable if it is a neighbor of the current Node, if there is line of sight between corresponding Points in the origin and destination PointSets, all Points in the destination PointSet, and if there are no collisions in the destination PointSet. This is not the best definition, but it's a WIP.

## Functionality
<p>A Point class that is agnostic to grid size. The Point class provides ways to calculate distances, rotations, and neighbors. It has constructors for the FVTT Token class, pixel coordinates, or Point coordinates. It can also return a set of contiguous Points if a token has width/height > 1.</p>

<p>A PathManager class that provides two ways of calculating paths. First, there are the two static methods: one that finds a path between two Points and one that finds paths from a block of configuration data. However, it also provides a way of finding paths between tokens. The addToken method finds and stores the path between two tokens, origin and target.</p>

<p>A Path class that uses A* to find the optimal path. Path also provides convenient methods to help make use of the data.</p>

<p>Line of sight and collision detection. The A* algorithm is implemented such that tokens will not move through walls or on top of other tokens.</p>

<p>This module supports tokens with dimension > 1.</p>

<p>This module can calculate a path for a token and then move that token along that path.</p>

## Future Improvements
<p>I plan to add support for hex tiles, custom heuristics, different priority measures, settings, and better handling of collision, line of sight, attack range, and failure to find paths.</p>
