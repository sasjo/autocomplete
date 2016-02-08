# Autocompletion in Swing

This project was created 18th October 2009. Below is the original accompanying blog post. 

Originally published at http://samuelsjoberg.com/archive/2009/10/autocompletion-in-swing (dead link).

This project is in the public domain.

------

In a small application I developed not long ago I wanted to use autocompletion directly inside a text field. The completion was used in a multiline JTextArea and therefore I didn't want any drop-down list displaying the suggestions (this is the approach taken by most existing solutions). Instead I wanted the suggestion to appear as a selected text range when a unique suggestion was found.

![Selected auto completion](https://github.com/sasjo/autocomplete/blob/master/autocomplete.png)

The picture above illustrates the behavior I was looking for. As the user starts to type, additional (completed) characters should be selected. If the user type another character, the suggested completion will be removed and the completion evaluation cycle executes again with the newly added character.

I also decided that erasing a character should not trigger a new completion lookup. Only inserts should result in completions. This behavior is consistent with for example the URL address field in Safari.

## A generic solution

My goal was to build something generic that can be reused among all JTextComponents. To achieve this, I decided to make an autocompleting Document model. The Document is the model in a JTextComponent, containing the characters.

Let's walk through the provided sample to see how the solution works (source code is attached below).

```java
// Create the completion service.
NameService nameService = new NameService();
```

First thing we need to do is to create the `CompletionService`. This service will be used by our `AutoCompleteDocument` to lookup completions for the already typed characters.

In the sample, we'll use a simple service that offers autocompletion of popular baby names.

```java
// Create the input field.
JTextField input = new JTextField();

// Create the auto completing document model with a
// reference to the service and the input field.
Document autoCompleteDocument = new AutoCompleteDocument(nameService, input);
```

The next step is to create a text field. After that, we create our `AutoCompleteDocument` and supply it with a reference to the `nameService` and the text field itself.

The last step required is to set the `AutoCompleteDocument` on the text field:

```java
input.setDocument(autoCompleteDocument);
```

That's it. Your done!

But wait, this is not entirely true. You'll also need to provide an implementation of the `CompletionService`. This is the interface to implement:

```java
public interface CompletionService<T> {

    /**
     * Autocomplete the passed string. The method will return the matching
     * object when one single object matches the search criteria. As long as
     * multiple objects stored in the service matches, the method will return
     * <code>null</code>.
     *
     * @param startsWith
     *            prefix string
     * @return the matching object or <code>null</code> if multiple matches are
     *         found.
     */
    T autoComplete(String startsWith);
}
```

My idea is that the `CompletionService` interface is implemented as part of the lookup services that e.g. hit the database to look up things. Other possibilities is to build a wrapper service that is populated with a list of possible completions.

The intention is to have a flexible way of providing autocompletion that is properly decoupled from the Swing implementation details.

I'm releasing this to the public domain, without any warranty or support. If you find this useful, I'd gladly hear about it though!

