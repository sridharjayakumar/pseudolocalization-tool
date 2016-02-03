# Introduction #

This document describes the structured message API, which allows localization of complex messages.  You will need to use this API to integrate an external message system or add new import/export formats.

# Message Representation #

## Interfaces ##

The following interfaces define the basic API.

### Message ###

A message is composed of a sequence of one or more fragments, all subclasses of **MessageFragment**.

### TextFragment ###

A fragment containing normal, localizable text.

### NonlocalizableTextFragment ###

A fragment containing text that should not be localized, such as HTML or format patterns.

### Placeholder ###

A fragment representing a value which will be substituted at runtime, such as a total or a value from a database.

### VariantFragment ###

A variant fragment represents multiple options for a portion of the message, one of which will be chosen at runtime.  For example, plural forms or gender selection will use this.

A VariantFragment is composed of one or more VariantSelectors, which define a way of choosing one of several VariantForms.  Each VariantForm contains zero or more MessageFragments (which can in turn contain other VariantFragments, as well as any other fragment type).

## Abstract Implementations ##

If you can alter the inheritance hierarchy of your message classes, you can save some work by using the abstract implementation classes of the above interfaces.  Even if you can't use them directly, you can still look at the source to see what is required of implementations.

# Visitor API #

The visitor API will be used for building and processing your structured messages, as well as writing a new pseudolocalization method.  All methods receive a `VisitorContext` object, which can be used to mutate the message as it is being visited.

Typical users will extend `DefaultVisitor`, which implements all the interfaces and does nothing on each visitor call, and then override just the methods they are interested in.

## MessageVisitor ##

The top level visitor is `MessageVisitor`, which is passed to `Message.accept`.

### `MessageFragmentVisitor visitMessage(VisitorContext, Message)` ###

Called when beginning a visit on a Message.  Returns a MessageFragmentVisitor to visit fragments of the message, or null if no further visitation is required.

### `void endMessage(VisitorContext, Message)` ###

Called after visiting all fragments of a Message.

## MessageFragmentVisitor ##

A MessageFragmentVisitor is called for each fragment in a message.

### `void visitNonlocalizableTextFragment(VisitorContext, NonlocalizableTextFragment)` ###

Visits a `NonlocalizableTextFragment`.

### `void visitPlaceholder(VisitorContext, Placeholder)` ###

Visits a `Placeholder`.

### `void visitTextFragment(VisitorContext, TextFragment)` ###

Visits a `TextFragment`.

### `VariantFragmentVisitor visitVariantFragment(VisitorContext, VariantFragment)` ###

Visits a `VariantFragment`.  Returns a `VariantFragmentVisitor`, or null if there is no need to visit the internal structure of the the variant fragment.

## VariantFragmentVisitor ##

This visitor visits the structure of a variant fragment.

### `VariantFormVisitor visitSelector(VisitorContext, VariantSelector )` ###

Visits a `VariantSelector`.  Returns a `VariantSelectorVisitor`, or null if there is no need to visit the forms and message fragments underneath this selector.

### `void endSelector(VisitorContext, VariantSelector)` ###

Called after completing the visit of a `VariantSelector`.

### `void endVariantFragment(VisitorContext, VariantFragment)` ###

Called after all selectors in a variant fragment have been visited.

## VariantFormVisitor ##

This visitor visits form of a variant fragment.

### `MessageFragmentVisitor visitVariantForm(VisitorContext, VariantForm)` ###

Visit a variant form, and returns a visitor to use for visiting the associated message or null if no visit is required.

### `void endVariantForm(VisitorContext, VariantForm)` ###

Called after visiting a variant form is complete.