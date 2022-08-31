# TDD

All right. So one of the problems is that when Bob the park ranger sees the
dinosaur size... he can't remember if these are in meters... or centimeters...
which makes a big difference when you step into a cage.

A better way might be to just use words like small, medium, or large. So... let's
do that!

*But*, to add this feature, we're going to use a philosophy called
test driven development or TDD. TDD is basically a buzzword that describes a
4-step process for writing your test *before* the feature.

Step 1: write a test for the feature. Step 2: run your test and watch it fail...
since you haven't created that feature yet! Step 3: Write as little code as possible
to get your test to pass. And Step 4: now that it's passing, refactor your code
if needed to make it more awesome.

So, to get the Small, Medium or Large text, I think we should add a new
`getSizeDescription()` method to our `Dinosaur` class. *But*, remember, we're
going to do this the TDD way, where step 1 is to write a *test* for that method...
even though it doesn't exist yet. Yes, I know that's weird... but it's kinda awesome.

Add `public function` and let's first test that a dinosaur that's over 10 meters
or greater is large. Inside, say `$dino = new Dinosaur()`, give him a name,
let's use Big Eaty again, he's a cool dude, and set the length to 10.

Then, `assertSame()` that `Large` will be equal to `$dino->getSizeDescription()`.
For our failure message, lets use This is supposed to be a large Dinosaur.
Yes, we're *literally* testing a method that doesn't exist yet. That's TDD.

Ok, step 1 of TDD is done. Step 2 is to run our test and make sure it fails.
Open up a terminal and then run `./vendor/bin/phpunit` and also add the
`--testdox` flag.

And... great! 2 tests, 4 assertions, and 1 error.
Our new test failed because, of course, we called an undefined
method! We kind of knew
this would happen. I'll explain why in a minute, but notice that our "this is
supposed to be at large dino" message isn't showing up here.

Time for step 3 of TDD: write simple code to make this test pass.
This part, taken literally, can get kinda funny. Watch:
back in our Dinosaur class add a new `public function getSizeDescription()`
which will return a `string`. Inside... `return 'Large'`. Yup, that's it!
Move back to your terminal and re-run the tests.

```terminal-silent
./vendor/bin/phpunit --testdox
```

And... Awesome - They Pass! Well... of *course* the test passed - we hardcoded
the result we want! But, that's *technically* what TDD says: write the *least*
amount of code possible to get your test to pass. If your method is too simple
after doing this, it means you're missing more tests - like for small or medium
dinosaurs - that would force you to *improve* the method. We'll see that in a minute.

But let's be a *bit* more realistic. Say:
`if ($this->length >=10) {`, then `return 'Large'`. Run the tests *one*
more time to make sure they're still passing:

```terminal-silent
./vendor/bin/phpunit --testdox
```

And... yes! We're still good to go!

Before we move onto the *last* step in TDD, I think we need to add a couple more
size description tests. In our `DinosaurTest::class` copy our
`testDino10MetersOrGreaterIsLarge` method and rename it to
`testDinoBetween5And9MetersIsMedium()`. Inside, change the
`length` of our `$dino` from `10` to `5` and for the assertion, change that
to `Medium`. Update the message to `Medium` as well.
*Finally*, for our small dino test, paste the method again,
rename it to `testDinoUnder5MetersIsSmall()`, set the
length to `4`, assert that `small` is equal to `getSizeDescription()` and *also*
update the message.

Back in our terminal, run the tests again:

```terminal-silent
./vendor/bin/phpunit
```

And... they're failing! But not because our method returns the wrong result. They're
failing due to a type error on `getSizeDescription()`:

> The return value must be of type string and none is returned.

Do you remember earlier we ran our large dinosaur
test *before* writing the method and we didn't see our "this is supposed to be
a large dino" message? Well, we don't see it here either... That's
because PHP threw an error... and so the `getSizeDescription()` message explodes
*before* the test can run the `assertSame()` method. It's no big deal and we can
still use the stack trace to see exactly where things went wrong.

Alrighty, back to the `Dinosaur` class. Lets fix these tests by adding
`if ($this->length)` is less than `5`, `return 'Small'`. And
`if ($this->length)` is greater than `10`, `return 'Medium'`

Back to our terminal,
run the test again:

```terminal-silent
./vendor/bin/phpunit --testdox
```

And... alright alright alright... they're passing.

So let's move on to the *last* step of TDD... and a fun one! Refactoring our code.

Looking at our `getSizeDescription()` method, I think we can clean this up a bit.
And the great news is that, because we've covered our method with tests, if we
mess something up during refactoring, the tests will tell us! We get to be reckless!

Let's change this middle condition to `if ($this->length)` is greater than or equal
to `5`, return `Medium`. We can get rid of this last conditional
all together and just return `Small`.

I like that! To see if we messed up, move back to the terminal and run our
tests again.

```terminal-silent
./vendor/bin/phpunit --tesdox
```

And... we've done it! That's TDD - write the test, see the test fail,
write simple code to see the test pass, then refactor our code. Rinse and repeat.

And now that we have our fancy new method - built via the powers of TDD - let's
celerbate by *using* it on the site!

Close up our terminal and move to our template: `templates/base.html.twig`.
Instead of showing the dino's with `dino.length`, change
to `dino.sizeDescription`. Save it, go back to our browser and.. refresh.
Awesome. We have large, medium and small for the dinosaurs size instead of a number.
No way Bob will accidentally wander into the T-Rex enclosure anymore!

We've just used TDD to make our app a bit more human friendly. Coming up next,
we'll use some of the TDD principles we've learned here to clean up our tests with
PHPUnit's data providers!