#Testing Android
-----------------------------------

Test Driven Development with Android can be hard, especially when you’re just starting out.

Android was my first experience of mobile development and, as I was creating my first application, I found it increasingly hard to separate my code from Android elements. This, in turn, made it increasingly hard to test drive my application.  Everything was triggered by some kind of event, and I was finding it difficult to separate my conceptualization of the business logic from the physicality of that event, whether it was the click of a button or a touch on the screen.  As a result, I began to rely on tests that carried out that behaviour.

These application tests run using the emulator in order to perform those clicks and touches and, for high level tests, can be very useful. Using them to try to test drive the app's development, however, is neither useful nor pleasant.  Each time these tests are run the app is reinstalled onto the emulator before the tests carry out the actions defined for them, and this is a very slow process.

I had two key problems:

1. **Emulator Tests**

	When the feedback loop becomes this long and arduous, tests cease to feel like a useful tool and more like a chore, but that is not the only downside to trying to use application tests this way.  I was constantly trying to test from the top and my business logic became tangled with the Android framework as a result.  There were no clear separations and my tests were not asking for any.  Doing the simplest thing to get into the green from the top is a good way to end with the majority of code being written in that level.  I had all the tools I needed to properly test drive the app's development, and I had chosen the wrong tool for the job.

	Because I was relying so heavily on tests that ran on the emulator, the feedback loop was slow.  Although I was trying to keep to the red, green, refactor loop, slow tests make this hard.  A slow feedback loop is incredibly detrimental to TDD.  Testing should be easy, it should be fast, and it should be simple.

	> Make it work. Make it right. Make it fast.

	As well as slow, the tests that ran on the emulator were unpredictable.  Reinstalling the application onto the emulator is a heavy duty activity, and that means the tests could hang if there was another program fighting for CPU.  Sometimes they would simply quit before running.  Sometimes they would run in less than five minutes, sometimes they would run in over ten.  This is not a problem specific to Android or Android Studio, it's simply a fact of working with emulators, and it makes for a difficult workflow.

  Failing fast is one of the major benefits of TDD for me.  If I can write the right test, see it fail and make it green in the very simplest way possible, then it's unlikely that the design that emerges will be over complicated or convoluted.  If I have to wait to even see the test fail, then before I've seen the failure, before I know the message, I'm thinking about how to make it pass.  The design does not emerge, I already have an idea in place by the time I go to write the solution.  This is not how TDD should be done, and negates many of the benefits of a test driven approach.


2. **Tightly Coupled Code**

	There are many reasons why you should decouple your code and the framework you use. Decoupled code is reusable, easier to maintain, easier to extend, easier to test, easier to debug...the list goes on.  Regardless of the framework, you want distance between the code you write and the code someone else has written for you.

  My code was so close to Android that I was having to learn far more about the intricacies of Android than I should have had to. If something went wrong or did not behave as I had expected, I was having to look into Android code and my own.  Was I misunderstanding how a particular feature of Android worked, or was there a bug in my code?  The two being so closely tied meant there was not a simple answer.  If my code was removed from Android, I would only have one point at which things could go wrong.

	If your code is tightly coupled to the framework you use, you are also forced to use a testing framework and test runner that accommodates for that framework.  Decoupled code frees you from this, allowing you to use a test framework that is inevitably simpler and, especially in the case of Android, faster. 

The solution to both of these problems was the same:

**Abstract business logic**

Decoupled code is a core concept, one I was well aware of, and yet was struggling to implement. Perhaps it was the novelty of working with a new framework, perhaps it was the novelty of working with Android itself, perhaps I simply had not understood how to decouple code well enough, but I found that often my logic was too tied to the click of a button.  Whatever the reasons, I was finding it hard to distance myself from the elements of Android, and I needed to come up with a way to overcome this using what I had available to me. I had the tools I needed to find to a solution, I just needed to use them properly.

1. **TDD**

	A feature of test driven development is that you don't have to worry about whether or not your code is easy to test.  If all of your production code is written as a result of a test, it will be easy to test. Considering the code I was writing was becoming harder and harder to test, it became obvious that I had an issue with my test workflow.  This issue lay with the fact that I was not using unit tests in order to drive my code, I was using high level tests that only tested the outcome on the application level.  They did not care about the details of implementation.  Of course, it is not the job of an application test to care about the implementation details, and this was the key to my mistake.

2. **Test Doubles**

	All of the Android elements that I was interacting with are, at heart, Java objects and interfaces.  Because of this, they can be extended or implemented into custom objects that can take the form of any test double, just like a plain Java object.  If I could separate my business logic from Android, then I would still be able to test drive the integration using spies and fakes.

**Android and TDD**

An tool that helped me separate my logic from Android was, perhaps conversely, a more Outside In approach to TDD. I knew what I wanted to happen when certain actions in the application happened, the issue was how to implement the behaviour without depending so heavily on Android.

I usually use TDD from the Inside Out.  I prefer to start with the more granular components, building them up until I am ready to integrate them.  With Android though, I was finding this hard.  I could not immediately see the granular level.  I was blinded by the big picture, and couldn't see the details that made it up.  I had tried, and failed, to address this.  I was fixated on the big picture, and I had to find a way of testing that accommodated for that if I was going to make any progress.

Using a more outside in approach allowed me to indulge this global thinking. It allowed me to write a test that defined the behaviour I had in mind, implement it enough to get the test to pass and then build up the more granular objects that emerged from the initial implementation until, finally, I could integrate the granular with the global. I could write a high level feature test that defined the behaviour I wanted to see, button clicks and finger presses included, and from there, I could fill in the blanks with plain Java.

For example, when building a simple counter application where the press of a button triggers the text of that button to be replaced with an incremented number, I could write a failing high level espresso test like the following:

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

Making this pass involves two steps.  A button needs to be created corresponding to the id, with an initial text of `0`  and an `OnClick` method:

```
<Button
	android:layout_width="wrap_content"
	android:layout_height="wrap_content"
	android:id="@id/number_button"
	android:onClick="increaseButton"
	android:text="0"/>
```

Then this button needs to be connected to the Activity with the `increaseButton` method:

```
public class CounterActivity extends AppCompatActivity {

  @Override
  public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_counter);
  }

  public void increaseButton(View view) {
	  Button button = (Button) view;
	  button.setText("1")
  }
}
```

With the high level test passing, the elements needed in order to create the implementation become clear.  There are two distinct elements here: something to increase a number and something to change the text of the button when the button is pressed.  Once these objects are defined, the goal becomes to test drive their development. This helped me greatly in separating Android the business logic because, once I knew the objects I needed, I knew that I should not have to touch the Activity class again until these objects had the behavior I expected, and simply needed to be integrated.

**Android and Test Doubles**

After the high level test is written, it becomes simpler to define the objects needed to implement the behaviour we want.  After that point, it becomes a question of how to test drive the development of those objects and how to keep them separate from Android. 

When integrating, for example, a custom button with an Activity class, test doubles can help.  Say we have a button that, when clicked, changes text, we could test drive a custom onClickListener with the functionality to set the text using test doubles.

```
public class ExampleClickListenerTest {
	@Test
	public void setsTheButtonText() {
		ButtonSpy buttonSpy = new ButtonSpy();
		ExampleClickListener exampleClickListener = new ExampleClickListener();

		exampleClickListener.click();

		assertTrue(exampleButton.setTextWasCalled);
	}
}
```

In order to create a button spy we need to first wrap the Android `Button` class in a custom object, since, like many methods in Android classes, the `setText` method is final and so cannot be overridden.  The base class would look like this:

```
public class ExampleButton extends Button {
	public ExampleButton(Context context) {
		super(context);
	}

	public void setText(String text) {
		super.setText(text);
	}
}
```

With the custom button in place, we can create the spy:

```
public class ButtonSpy extends ExampleButton {

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

Here we are instantiating the custom button with a `null` value.  An Android button takes an instance of `Context` but because we are not testing any of the methods that use context, null acts as a dummy.

For the test to compile, we need an `ExampleClickListener`:

```
public class ExampleClickListener implements OnClickListener {

	@Override
	public void onClick(View view) {
	}

}
```

And once it has compiled, we can make it pass:

```
public class ExampleClickListener implements OnClickListener {

	@Override
	public void onClick(View view) {
		ExampleButton exampleButton = (ExampleButton) view;
		exampleButton.setText("clicked!");
	}
}
```

In a similar manner, the majority of Android elements can be test driven using doubles, as can the integration of all the elements that make up the application you build.

As my experience with Android has grown, I've found it easier to see the granular elements of an application and, as a result, have found it easier to use a more familiar inside out TDD approach.

My first experience with Android was a challenge, however.  For whatever reason, I could not step back from the big picture.  I was too involved with Android, and because of this, I started on the wrong foot. My usual way of building an application up from the bottom up simply was not working, and it took me a while to adapt. Once I recognised that I was thinking about Android in a different way to how I usually think about an application, and I recognised that I had to allow for that, I was able to use what I already knew to facilitate a changed way of working.

Sometimes the way we are used to working is challenged.  Sometimes it simply isn't working for us.  It could be a new framework, it could be a new language, it could be a new environment, it could just be that we are approaching a problem in a different way.  Whatever the reason, to continue progressing we have to be open to work in different ways, and know that, often, we already have the tools we need to do so.
