title: Second post
date: 2013-10-22 22:00:00
tags: test
---

This a second post.

``` [java]
package exercise.instructions;

import exercise.AbstractInstruction;

/**
 * Instruction add "+"
 *
 * @author foo
 *
 */
public class Add extends AbstractInstruction {

    public Add(int argument) {
        super(argument);
    }

    @Override
    public int excute(int param) {
        return param + getArgument();
    }

}
```
