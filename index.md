#Testing Android
-----------------------------------

Test Driven Development with Android can be hard, especially when you’re just starting out with the Android OS.

Android was my first experience of mobile development and, as I developed my first application, I found it increasingly hard to separate my code from the Android elements. This, in turn, made it increasingly hard to test drive my application.  Everything in the application was triggered by some kind of event, and I found it difficult to seperate my conceptualization of the business logic from the physicality of that event; the click of a button or a touch on the screen.  As a result, I began to rely on tests that carried out that behaviour.

These application tests run using the emulator in order to perform those clicks and touches and, for high level tests, can be very useful. Using them to try to test drive the app's development, however, is neither useful nor pleasant.  Each time these tests are run the app is reinstalled onto the emulater before the tests carry out the actions defined for them. This is a very slow process.

I had two key problems:

1. **Emulator Tests**

	When the feedback loop becomes this long and arduous, tests cease to feel like a useful tool and more like a chore, but that is not the only downside to using application tests this way.  I was constantly trying to test from the top, from the application level, and my business logic became tangled with the Android framework as a result.  There were no clear separations because my tests were not asking for them.  Doing the simplest thing to get into the green from the application level is usually going to end with the majority of code being written in that level.  I had all the tools I needed to properly test drive the app's development, and I had chosen the wrong tool for the job.

	Because I was relying so heavily on tests that ran on the emulator, the feedback loop was slow.  Although I was trying to keep to the red, green, refactor loop, slow tests make this hard.  A slow feedback loop is incredibly detrimental to TDD.  Testing should be easy, it should be fast, and it should be simple.

	> Make it work. Make it right. Make it fast.

	As well as slow, the tests that ran on the emulator were unpredictable.  Reinstalling the application onto the emulator is a heavy duty activity, and that means the tests would sometimes hang if there was another program fighting for CPU.  Sometimes they would simply quit before running.  Sometimes they would run in less than five minutes, sometimes they would run in over ten.  This is not a problem specific to Android or Android Studio, it's simply a fact of working with emulators.

  Failing fast is one of the major benefits of TDD for me.  If I can write the right test, see it fail and make it green in the very simplest way possible, then it's unlikely that the design that emerges will be overcomplicated or convuled.  If I have to wait to even see the test fail, then before I've seen the failure, before I know the message, I'm thinking about how to make it pass.  The design does not emerge, I already have an idea in place by the time I go to write the solution.  This is not how TDD should be done, and negates many of the benefits of a test driven approach.


2. **Tightly Coupled Code**

	There are many reasons why you should decouple your code and the framework you use. Decoupled code is reusable, easier to maintain, easier to extend, easier to test, easier to debug...the list goes on.  Regardless of the framework, you want distance between the code you write and the code you have to use.

  My code was so close to Android that I was having to learn far more about the intricacies of Android than I should have to. If something went wrong or did not behave as I'd expected, I was having to look into Android code and my own.  Was I misunderstanding how a particular feature of Android worked, or was there a bug in my code?  THe two being so closly tied it was nto a simple answer.  If my code was removed from Android, I would only have one point at which things could go wrong.

	If your code is tightly coupled to the framework you use, you are also forced to use a testing framework and test runner that accommodates for that framework.  Decoupled code frees you from this, allowing you to use a test framework that is inevitably simpler and, especially in the case of Android, faster.

The solution to both of these problems was the same:

**Abstract business logic**

Decoupled code is a core concept, one I was well aware of, and yet was struggling to implement. Perhaps it was the novelty of working with a new framework, perhaps it was the novelty of working with Android itself, perhaps I simply had not understood how to decouple code well enough, but I found that often my logic was too tied to the click of a button.  Whatever the reasons, I was finding it hard to distance myself from the elements of Android, and I needed to come up with a way to overcome this using the tools I had available to me. I had the tools I needed to find to a solution, I just needed to use them properly.

1. **TDD**

	A feature of test driven development is that you don't have to worry about whether or not your code is easy to test.  If all of your production code is written as a result of a test, it will be easy to test. Considering the code I was writing was becoming harder and harder to test, it became obvious that I had an issue with my test workflow.  This issue lay with the fact that I was not using unit tests in order to drive my code, I was using high level tests that only tested the outcome on the application level.  They did not care about the details of implementation.  Of course, it is not the job of an application test to care about the implementation details, and this was the key to my mistake.

2. **Test Doubles**

	All of the Android elements that I was interacting with are, at heart, Java objects and interfaces.  Because of this, they can be extended or implemented into custom objects that can take the form of any test double, just like a plain Java object.  If I could separate my business logic from Android, then I would still be able to test drive the integration using spys and fakes.

**Android and TDD**

An approach that helped me separate my logic from Android was a kind of Outside In. I knew what I wanted to happen when certain actions in the application happened, the issue was how to implement the behaviour without depending on Android.

I usually use TDD fom the inside out.  I prefer to start with the more granular components, building them up until I am ready to integrate them.  With Android though, I was finding this hard.  I could not immediately see the granular level.  I was blinded by the big picture, and couldn't see the details that made it up.  I had tried, and failed, to address this.  I was fixated on the big picture, and I had to find a way of testing that accomodated for that if I was going to make any progress.

Using a more outside in approach allowed me to indulge this global thinking. It allowed me to write a test that defined the behaviour I had in mind, implement it enough to get the test to pass and then build up the more granular objects that emerged from the initial implementation until, finally, I could integrate the granular with the global. I could write a high level feature test that defined the behaviour I wanted to see, button clicks and finger presses included, and from there, I could fill in the blanks with plain Java.

**In Conclusion**

As my experience with Android has grown, I've found it easier to see the granular elements of an application and, as a result, have found it easier to use a more familiar inside out TDD approach.  

My first experience with Android though, as I have described, was a challenge.  For whatever reason, I could not step back from the big picture.  I was too involved with Android, and because of this, I started on the wrong foot. My usual way of building an application up from the bottom wasn't working, and it took me a while to adapt. Once I recognised that I was thinking about Android differently to how I am used to thinking about an application, and I recognised that I had to allow for that, I was able to use what I already knew to facilitate a changed way of working.  Sometimes the way we are used to working is challenged.  Sometimes it simply isn't working for us.  It could be a new framework, it could be a new language, it could be a new environment, it could just be that you're approaching a problem in a different way.  Whatever the reason, to continue progressing we have to be open to work in different ways, and know that we already have the tools we need to facilite progress.


An Example
----------------

I can demonstrate my approach with a simple Counter Application.  When thinking about a counter application, the behaviour we wish to see in the application is evident.  When a button a pressed, we wish for the initial number on screen to increase, and so, without too much thought about how to implement this, we can write the first high level test.

Assuming we've created a new Android project using Android Studio, with the domain `example` and the name `counter`, and using espresso to write the application tests,  I can write a simple test to encapsulate the behaviour I wish to see in `app/src/androidTest/java/example/counter/CounterActivity.java`

**The First Test**

```
@RunWith(AndroidJUnit4.class)
public class CounterActivityTest {

  @Rule
  public ActivityTestRule<CounterActivity> mActivityTestRule = new ActivityTestRule(CounterActivity.class);

  @Test
  public void numberButtonIncreasesWhenPressed() {
    onView(withId(R.id.number_button)).check(matches(withText("0")));
    onView(withId(R.id.number_button)).perform(click());
    onView(withId(R.id.number_button)).check(matches(withText("1")));
  }
}
```

The espresso syntax here can be a little confusing, but this test is simply expressing what I've already defined to be the behaviour of the application.  I assert that there is a button with the text 0, tell the android test runner to click that button and assert that the text on the button is now 1.

In order to make this compile all I need is a button with the id number_button. In `CounterActivity` I can see which layout file is being loaded:

```
public class CounterActivity extends AppCompatActivity {
  @Override
  public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_counter);
  }
}
```
So in `res/layout/activity_counter.xml` I can add a button element to the view with the id number_button and an initial text value of 0

```
<Button
android:layout_width="wrap_content"
android:layout_height="wrap_content"
android:id="@id/number_button"
android:text="0"/>
```

Now that the test compiles, I can see it fail.  It fails, as expected, when checking the text of the button now reads 1.

I want this test to pass before moving on to unit test the actual implementation, which can be done with an `OnClickListener` that simply changes the text of the button to 1

```
public class CounterActivity extends AppCompatActivity {

  @Override
  public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_counter);

    Button button = (Button) findViewById(R.id.number_button);
    button.setOnClickListener(new View.OnClickListener() {
      @Override
      public void onClick(View view) {
        Button button = (Button) view;
        button.setText("1");
      }
    });
  }
}
```

With this, the test will pass.  This is, of course, not the behaviour we want our final application to have, but it offers a place to start.  Now that we have defined how the text should change and why, we can move away from Android to fill in the blanks. Because the behaviour of the application has been defined, I can focus on test driving that behaviour and then focus on test driving the integration of that behaviour.  I should not have to touch the Activity class again until I have all the elements I need to execute that behaviour.

In making this test pass, I have already identified two distinct objects.  The first is something that increases the number, the second is something that uses that incrementer to update the button text.

**Unit Testing the Incrementer**

The first characterstic of the application is increasing a number, which can easily be seperated from Android.

In `app/src/test/java/example/Counter/IncrementerTest.java` I can start to test drive this new behaviour.

```
public class IncrementerTest {

  @Test
  public void incrementsZeroToOne() {
    Incrementer incrementer = new Incrementer();

    int number = incrementer.increase(0);

    assertEquals(1, number);
  }
}
```

In order for this test to compile, all I need is an Incrementer Class with an increase method in `app/src/main/java/example/Counter`

```
public class Incrementer {

  public int increase(int number) {
    return 0;
  }
}
```

Once the test compiles it can run and fail, and then I can make it pass:

```
public class Incrementer {

  public int increase(int number) {
    return 1;
  }
}
```

With the test passing, another test is needed to drive the real behaviour of the Incrementer.

```
  @Test
  public void incrementsOneToTwo() {
    Incrementer incrementer = new Incrementer();

    int number = incrementer.increase(1);

    assertEquals(2, number);
  }
```

Again, the test fails, and the new behaviour can be added to the Incrementer

```
public class Incrementer {

  public int increase(int number) {
    return ++number;
  }
}
```

So now that the Incrementer behaves as expected, the question becomes how to we test drive the integration whilst maintaining a separation from Android.

**Integrating With Android**

There are a few elements the application needs integrated:

1. That clicking the button calls the `increase` method
2. That clicking the button updates the button text
3. That `increase` is called with the correct value

How can we abstract these from the emulator?  How can we mimic the clicking of a button?  The answer lies with Test Doubles.

The first of these points of integration is that the `OnClickListener` needs to have access to the `increase` method.

```
public class IncrementerClickListenerTest {

  @Test
  public void callsIncrease() {
    IncrementerSpy incrementerSpy = new IncrementerSpy();
    IncrementerClickListener incrementerClickListener = new IncrementerClickListener(incrementerSpy, 0);

    IncrementerClickListener.click(new ButtonDummy());

    assertTrue(incrementerSpy.increaseWasCalled);
  }
}
```

In order to get this test to compile, we need to create a few classes, the first is an `IncrementerSpy`:

```
private class IncrementerSpy extends Incrementer {

  public boolean increaseWasCalled;

  @Override
  public int increase(int number) {
    increaseWasCalled = true;
    return 0;
  }
}
```

The second class is a `ButtonDummy`.  Here we are writing a double for an Android Element. There are many mocking frameworks to do this for you, but since all Android elements are Java objects, they are usually simple enough to mock.

```
public class ButtonDummy extends Button {
  public ButtonDummy() {
    super(null);
  }
}
```

The Android `Button` class usually expects an instance of `Context` to its initialiser, but as we are not going to use any of the methods that use `Context` we can pass it `null`

The third object needed is the `OnClickListener` we actually want to test:

```
public class IncrementerClickListener implements View.OnClickListener {

  public IncrementerClickListener(Incrementer incrementer) {
  }

  @Override
  public void onClick(View view) {
  }
}
```

With these objects in place, the test will run and fail, because the Incrementer we pass in is not having its `increase` method called.  Again, it is simple to make the test pass.

```
public class IncrementerClickListener implements View.OnClickListener {

	private Incrementer mIncrementer;

	public IncrementerClickListener(Incrementer incrementer) {
		this.mIncrementer = incrementer;
	}

  @Override
  public void onClick(View view) {
    mIncrementer.increase(0);
  }
}
```

The test passes and we can feel confident that the `OnClickListener` and `Incrementer` are working together. Next, the changing of the button's text can be tested, and we already have all the objects we need in order to test that behaviour.

The test looks like this:

```
  @Test
  public void changesButtonText() {
    ButtonSpy buttonSpy = new ButtonSpy();
    IncrementerClickListener incrementerClickListener = new IncrementerClickListener(incrementerSpy);

    IncrementerClickListener.click(buttonSpy);

    assertTrue(buttonSpy.setTextWasCalled);
  }
```

I cannot override the `setText` method in the Android Button because it is final, so in order to be able to create a spy with the method, I first need to wrap the Button object in a custom button:

```
public class NumberButton extends Button {
  public NumberButton(Context context) {
    super(context);
  }

  public void setText(String text) {
    super.setText(text);
  }
}
```

The ButtonDummy from the first test only needs a little modification in order to get this test to compile:

```
private class ButtonSpy extends NumberButton {

  public boolean setTextWasCalled;

  public ButtonSpy() {
    super(null);
  }

  @Override
  public void setText(String text) {
    setTextWasCalled = true;
  }
}
```

Once this modification is complete, the test will run and fail.  In order to make it pass, we have to make the `OnClickListener` interact with the button.

```
public class IncrementerClickListener implements View.OnClickListener {

  private Incrementer mIncrementer;

  public IncrementerClickListener(Incrementer incrementer) {
    this.mIncrementer = incrementer;
  }

  @Override
  public void onClick(View view) {
    mIncrementer.increase(0);
    NumberButton button = (NumberButton) view;
    button.setText("");
  }
}
```

The test now passes, but the behaviour we want is still not quite implemented.  There are some hard coded values in the `OnClickListener,` and if we were to integrate it with the `CounterActivity`, the very first test would fail.  We need tests to check whether we are passing the correct values to the `increase` method and the `setText` method:

```
  @Test
  public void increasesInitialValue() {
    IncrementerClickListener incrementerClickListener = new IncrementerClickListener(incrementerSpy, 0);

    IncrementerClickListener.click(new ButtonSpy());

    assertEquals(0, incrementerSpy.increaseArgument);
  }

```

With a few additions to the spy and the `IncrementerClickListener`, this test will compile.

```
private class IncrementerSpy extends Incrementer {

  public boolean increaseWasCalled;
  public int increaseArgument;

  @Override
  public int increase(int number) {
    increaseWasCalled = true;
    increaseArgument = number;
    return 0;
  }
}
```

```
public class IncrementerClickListener implements View.OnClickListener {

  public IncrementerClickListener(Incrementer incrementer, int number) {
    this.mIncrementer = incrementer;
  }

  @Override
  public void onClick(View view) {
    mIncrementer.increase(0);
    NumberButton button = (NumberButton) view;
    button.setText("");
  }
}
```

The first test will now compile, run and fail, but is simple to get passing:

```
public class IncrementerClickListener implements View.OnClickListener {

  private Incrementer mIncrementer;
  private int mNumber;

  public IncrementerClickListener(Incrementer incrementer, int number) {
    this.mIncrementer = incrementer;
    this.mNumber = number;
  }

  @Override
  public void onClick(View view) {
    mIncrementer.increase(mNumber);
    NumberButton button = (NumberButton) view;
    button.setText("0");
  }
}
```

Running the tests we'll see green, and there's just one more test to ensure we've got all the business logic we need:

```
  @Test
  public void setsTheButtonTextToTheIncreasedValue() {
    ButtonSpy buttonSpy = new ButtonSpy();
    IncrementerClickListener incrementerClickListener = new IncrementerClickListener(new IncrementerSpy(), 0);

    incrementerClickListener.click(buttonSpy);

    assertEquals("1", buttonSpy.setTextArgument);
  }
```

```
private class ButtonSpy extends NumberButton {

  public boolean setTextWasCalled;
  public String setTextArgument;

  public ButtonSpy() {
    super(null);
  }

  @Override
  public void setText(String text) {
    setTextArgument = text;
    setTextWasCalled = true;
  }
}
```

The test compiles and fails, so once again we can make it pass with a very small change:

```
public class IncrementerClickListener implements View.OnClickListener {

  private Incrementer mIncrementer;
  private int mNumber;

  public IncrementerClickListener(Incrementer incrementer, int number) {
    this.mIncrementer = incrementer;
    this.mNumber = number;
  }

  @Override
  public void onClick(View view) {
    mNumber = mIncrementer.increase(mNumber);
    NumberButton button = (NumberButton) view;
    button.setText(String.valueOf(mNumber));
  }
}
```

All the business logic we need for the application is now almost in place and independent of the emulator test runner, and all that is left is to integrate it with our activity.

We have our application test that defines the behaviour we want, and now that we have all the elements to implement that behaviour we can integrate them, knowing that all the elements work as expected.

We need to update the layout to use the new NumberButton first:

```
<NumberButton
android:layout_width="wrap_content"
android:layout_height="wrap_content"
android:id="@id/number_button"
android:text="0"/>
```

And then update the Activity:

```
public class CounterActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_counter);
        NumberButton numberButton = (NumberButton) findViewById(R.id.number_button);
        numberButton.setOnClickListener(new IncrementerClickListener(new Incrementer(), 0));
    }
}
```

When we run the Application Test, it will still pass, and we are confident that all the elements used work exactly as we expect.

