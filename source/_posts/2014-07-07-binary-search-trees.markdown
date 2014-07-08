---
layout: post
title: "Traversing Binary Search Trees"
date: 2014-07-07 19:26:43 -0400
comments: true
categories: "Flatiron&nbsp;School"
---
Traversing a binary search tree can take as little as five lines of code, if you: a) know what you're doing; b) know a bit about recursion; and c) know a bit more about block parameters:

```ruby
def each(&block)
 left.each(&block) if left
 block.call(data)
 right.each(&block) if right
end
```

  1. An object (in this case, the parameter 'block') is passed into our method signature, prefixed with an ampersand.
  2. Ruby calls `to_proc` on 'block' (if it is not already a `Proc` object), since it saw the ampersand there.
  3. We use recursion to iterate as follows:
    1. `each` is called recursively on a node's left child, until the leftmost child is reached.
    2. When there is no further left child, that direct parent node is then yielded.
    3. `each` is then called recursively again, this time on that direct parent node's right children, if they exist.

In this way, an *ordered* list of all nodes within a binary search tree are properly yielded. There you go, that's it! You're all set. Well okay, let's back up a minute and inspect that `&` symbol, the *ampersand*.

## The Power of `&`

**We can use the ampersand in front of objects passed in to method calls or definitions (as arguments or parameters) in order to coax Ruby into treating the objects as blocks instead of objects.**

Almost everything in Ruby is an object. Blocks are one of the few exceptions. In the context of the above code snippet, we see the ampersand prefixed to the object in the parameter of the method signature. This tells Ruby to expect a block, which it will try to conform to by calling `to_proc` on the parameter. Although blocks are not objects, `Proc`s are, and function in many similar ways.

The ampersand also allows us to refer to our parameter by name within the method definition, as is necessary when recursing. This is especially powerful in our example above, as it allows us to use recursion in tandem with a block argument to recreate the `each` function within our `Binary Search Tree` class, yielding the data within each instance of the class as we iterate over all nodes in the tree.

## Ways Around Using Blocks

By contrast, take a look below at the same function without block parameters or recursion:

```ruby
def each
    node = self
    self.count.times do
      node = node.left while node.left && !node.left.yielded
      unless node.yielded
        yield node.data
        node.yielded = true
      end
      if node.right
        node = node.right
      else
        node = node.parent while node.parent && node.parent.yielded
        node = node.parent
      end
    end
  end
```

Confusing, huh? Sure, it's *possible* to write a traversal method that doesn't use blocks or recursion, but why would you want to? Without recursion, we are forced to:

* keep track of how many instances of the class we have (hence the `self.count`), in order to make sure we loop through enough times to hit every node in the tree.
* use conditional loops to ensure we return an ordered list, by drilling down to the leftmost subnode of whatever parent node we're currently on in our larger traversal.
* keep track of whether our current node has previously been yielded, as we already may have passed it on our way down into a right sub-tree.

## Wrapup and Resources

Blocks and recursion together provide a relevant and efficient means of recreating the `each` function for binary search trees, allowing us to traverse trees in a concise and neat manner.

* http://deatheragetr.github.io/blog/2013/09/14/binary-search-trees/
* http://www.skorks.com/2013/04/ruby-ampersand-parameter-demystified/