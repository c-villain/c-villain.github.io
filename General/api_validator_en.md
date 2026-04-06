# OpenAPI JSON Validation: Finding Broken API Responses via [apivalidator.ru](https://apivalidator.ru)

Agree, it would be convenient to have a simple tool that allows you to:
- upload a Swagger spec
- paste a real API response

and immediately understand:

- whether it’s valid
- and if not — why

Here it is — [apivalidator.ru](https://apivalidator.ru).

A simple service that helps you quickly check whether a real API response matches its specification and shows exactly where the problem is.

Recently, a colleague of mine told me about a problem he’s currently dealing with. I faced the same issue myself some time ago.
Honestly, I thought problems like this were long gone — but it turns out they’re not.

A few years ago, at a large company, we had a fairly typical but very frustrating situation.

The backend was a large legacy monolith, almost entirely written in PHP.
And as you know, PHP is not strictly typed — and as long as everything stays inside it, things mostly “work”.
The problems started when frontend teams began migrating to new networking layers based on code generation from Swagger.

And that’s when an issue surfaced that had previously gone unnoticed.
Due to the lack of strict typing, the same data could come in different formats.
For example, a numeric field could arrive either as a number or as a string ("12345").
Booleans were even more interesting. The same flag could come in multiple forms:

- "true" / "false"
- "1" / "0"
- 1 / 0
- true / false

In the old networking layer, this “worked”: values were simply coerced into the expected type until something fit.

The problem is that errors weren’t fixed — they were silently masked.
But with the new networking layer:
- objects failed to deserialize
- decoding broke
- in some cases, the entire JSON response failed
- client applications simply stopped working

Over time, we started cleaning up the backend:

- fixing types
- removing inconsistencies
- aligning on API contracts

However, one unpleasant issue remained.
Imagine an API returns an array of 100 objects:
99 are valid
1 is not
And that’s enough to break everything.

It’s especially painful when this happens on something like the main screen:
the UI doesn’t load, errors appear, things keep spinning or crashing — and the user gets no clear feedback.
At that point, the user doesn’t care which object is broken — they just leave.
From a development perspective, things aren’t much better.

You don’t know:
- which object is invalid
- where the type mismatch happened
- what exactly went wrong
And QA, of course, files an urgent bug on the frontend
(because “nothing works”).

At some point it became clear that we were missing a simple tool that would allow us to:
- take an API specification
- take a real server response
- and quickly validate them against each other
With a clear answer: where exactly the problem is and why it happens.
