# JavaScript

- [Σημειώσεις JavaScript](/docs/Courses/javascript.pdf)
- [Σημειώσεις JQuery](/docs/Courses/jquery.pdf)

---

## How to pass an ASP.net variable to a JavaScript function?

Passing an ASP.NET variable to a JavaScript function is quite easy. Regarding the type of variable, one may consider the following cases:

### A single variable

A single variable is the simplest case. The only requirement is that the variable must be visible to the JavaScript code and thus, we declare it other than private.

```
protected int test = 10;
```

Then, on the ASP.NET page:

```
<script>
  var t = "<%= test%>";
</script>
```

### An array

An array is a non-primitive data type. For passing it to a JavaScript variable, we first need to serialize it. Therefore, we declare a serializer.

```
protected int[] test = new int[3];
 
protected System.Web.Script.Serialization.JavaScriptSerializer serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
```

Then, in the ASP.NET page, we call the serializer before we pass the variable, like this:

```
<script>
    var t = "<%=serializer.Serialize(test) %>";
</script>
```

By default, the maximum length of the JSON string produced by the serializer is 2,097,152 characters. One can increase this length to its maximum value as follows:

```
jsSerializer.MaxJsonLength = 2147483647;
```

where the variable MaxJsonLength is of int32 type and 2147483647 is the maximum signed positive int32 number.