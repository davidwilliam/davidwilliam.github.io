---
layout: post
title: Metaprogramming in Ruby
date: 2022-02-26 01:09:00
description: Code that writes code
tags: metaprogramming ruby code
categories: programming
comments: true
og_image: /assets/img/metaprogramming.jpg
---

Ruby is a programming language created by <a href="https://github.com/matz">Yukihiro Matsumoto</a> (better known as Matz) a form of compilation of everything he liked the best about his favorite languages: <a href="https://www.perl.org/">Perl</a>, <a href="http://www.smalltalk.org/">Smaltalk</a>, <a href="https://www.eiffel.org/">Eiffel</a>, <a href="http://www.getadanow.com/">Ada</a>, and <a href="https://lisp-lang.org/">Lisp</a>. Matz was motivated to create a new language by balancing functional with imperative programming.

One of the first reactions people have when first interacting with Ruby is to say: "Wow, this is very simple!" Matz, however, states that his goal is to make Ruby <em>natural</em>, not <em>simple</em>. In fact, Matz remarks that "Ruby is simple in appearance, but is very complex inside, just like our human body."

In its official page, Ruby is described as a "dynamic, open source programming language with a focus on simplicity and productivity. It has an elegant syntax that is natural to read and easy to write." There is definitely much more that can be said about Ruby, even even in a introductory fashion, but this initial description is, in my view, spot on.

When I use Ruby, I am really not thinking about some of the mechanics of programming. Instead, I am mostly thinking about the result I am seeking to produce. Matz wanted Ruby code to be easily read by humans. In fact, Ruby code is meant to be very elegant and simple, which makes it my favorite language for <em>prototyping</em>.

# What Is Mettaprogramming?

If elegance, simplicity, and the natural aspect of its syntax are already great ingredients for prototyping, my favorite thing about Ruby is something yet more intriguing: <em>metaprogramming</em>!

Informally, metaprogramming is writing code that writes code. If you search online, this is the most popular definition of metaprogramming: "Code that writes code." Well, I don't like this definition. The reason is actually very simple. Consider the following C++ code:

{% highlight c++ linenos %}
// example.cpp

#include <iostream>
#include <fstream>
#include <string>
using namespace std;

int main()
{
  ofstream ofs("main.cpp");
  string code = "#include <iostream>\n"
                "using namespace std;\n\n"
                "int main()\n"
                "{\n"
                "  int a = 2;\n"
                "  int b = 3;\n\n"
                "  cout << \"a = \" << a << endl;\n"
                "  cout << \"b = \" << b << endl;\n"
                "  cout << \"a + b = \" << a + b << endl;\n"
                "  cout << \"a * b = \" << a * b << endl;\n\n"
                "  return 0;\n"
                "}";
  ofs << code;

  return 0;
}


{% endhighlight %}

When I run `g++ example.cpp -o example --std=c++11 && ./example`, a new file `main.cpp` will be created, which contains the following code:

{% highlight c++ linenos %}
// main.cpp

#include <iostream>
using namespace std;

int main()
{
  int a = 2;
  int b = 3;

  cout << "a = " << a << endl;
  cout << "b = " << b << endl;
  cout << "a + b = " << a + b << endl;
  cout << "a * b = " << a * b << endl;

  return 0;
}


{% endhighlight %}

When I run `g++ main.cpp -o main --std=c++11 && ./main`, I obtain:

```
a = 2
b = 3
a + b = 5
a * b = 6
```

This is a naive example of "code that writes code". Ok, maybe too naive, but the idea here is to illustrate the limitations of this popular definition of metaprogramming. A code that writes code is not interesting in itself. How code writes code and what you can do with that is a completely different story.

Paolo Perrotta wrote <a href="https://pragprog.com/titles/ppmetr2/metaprogramming-ruby-2/">a wonderful book</a> about metaprogramming in Ruby. Perrotta describes Ruby source code as "a world teeming with vibrant citizens including variables, classes, and methods." These citizens are <em>language constructs</em>. Therefore a more technical (and much more meaningful) definition of metaprogramming is <em>writing code that manipulates language constructs at runtime.</em> This is concept is so important that I will break it down for better visibility:

* What: writing code that manipulate language constructs.
* When: at runtime.


I like the second definition much better. Not every language can do that and the way Ruby achieves this dynamic manipulation of language constructs makes it incredibly elegant and powerful.

All I can do in a single blog post is to try to scratch the surface of metaprogramming in Ruby. For that, I invite you to take a look at five amongst other building blocks of metaprogramming in Ruby: Dynamic Dispatch, Dynamic Methods, Ghost Methods, Dynamic Proxy, and Blank Slate.

# Language Constructs


> For all the examples in this post, I used Ruby 3.1.0p0 (2021-12-25 revision fb4df44d16) [x86_64-darwin19].

When we create a class in Ruby, unless we decide otherwise, that class inherits properties and behaviors from other classes. These other classes are called "ancestors". They provide fundamental functionalities for any custom class in ther lineage. We can check what are the ancestors of my class as follows:

{% highlight ruby linenos %}
class MySimpleClass
end

MySimpleClass.ancestors
# it returns: [MySimpleClass, Object, PP::ObjectMixin, Kernel, BasicObject]

{% endhighlight %}

We can check what each of these ancestors are by checking their associated classes:

{% highlight ruby %}

MySimpleClass.ancestors.map(&:class)
# it returns: [Class, Class, Module, Module, Class]

{% endhighlight %}

We can also list what "fundamental functionalities" are inherited when we create a class in Ruby by executing:

{% highlight ruby %}

BasicObject.methods
# => [:allocate,  :superclass,  :subclasses,  :new,  :instance_method,  :public_instance_method,  :<=>,  :define_method,  :<=,  :>=,  :==,  :===,  :included_modules,  :include?,  :ancestors,  :attr,  :attr_reader,  :attr_writer,  :attr_accessor,  :instance_methods,  :public_instance_methods,  :protected_instance_methods,  :private_instance_methods,  :constants,  :freeze,  :inspect,  :const_set,  :const_get,  :const_source_location,  :const_defined?,  :class_variable_set,  :class_variables,  :remove_class_variable,  :class_variable_get,  :const_missing,  :class_variable_defined?,  :<,  :private_constant,  :>,  :singleton_class?,  :public_constant,  :deprecate_constant,  :prepend,  :include,  :module_exec,  :to_s,  :module_eval,  :class_exec,  :class_eval,  :pretty_print_cycle,  :remove_method,  :undef_method,  :alias_method,  :method_defined?,  :public_method_defined?,  :private_method_defined?,  :name,  :protected_method_defined?,  :public_class_method,  :private_class_method,  :autoload,  :autoload?,  :pretty_print,  :pretty_print_instance_variables,  :pretty_print_inspect,  :singleton_class,  :dup,  :itself,  :taint,  :tainted?,  :untaint,  :untrust,  :untrusted?,  :trust,  :methods,  :singleton_methods,  :protected_methods,  :private_methods,  :public_methods,  :instance_variables,  :instance_variable_get,  :instance_variable_set,  :instance_variable_defined?,  :remove_instance_variable,  :instance_of?,  :kind_of?,  :is_a?,  :display,  :hash,  :public_send,  :class,  :frozen?,  :tap,  :yield_self,  :then,  :extend,  :clone,  :method,  :public_method,  :singleton_method,  :define_singleton_method,  :=~,  :!~,  :nil?,  :eql?,  :respond_to?,  :object_id,  :send,  :to_enum,  :enum_for,  :pretty_inspect,  :__send__,  :!,  :instance_eval,  :instance_exec,  :!=,  :equal?,  :__id__]

{% endhighlight %}

You see a long list with methods that will be inherited by the classes/modules in the BasicObject's lineage. We can ask the list of methods for the module `Kernel`:

{% highlight ruby %}

Kernel.methods
# => [:puts,  :readline,  :readlines,  :p,  :Complex,  :Float,  :caller,  :caller_locations,  :set_trace_func,  :sprintf,  :format,  :Integer,  :String,  :Array,  :Hash,  :local_variables,  :fork,  :Pathname,  :exit,  :pp,  :warn,  :test,  :raise,  :gets,  :fail,  :global_variables,  :__method__,  :__callee__,  :__dir__,  :proc,  :lambda,  :eval,  :iterator?,  :block_given?,  :catch,  :throw,  :loop,  :sleep,  :rand,  :srand,  :trap,  :select,  :`,  :trace_var,  :untrace_var,  :load,  :at_exit,  :require_relative,  :require,  :autoload?,  :autoload,  :binding,  :Rational,  :exec,  :exit!,  :system,  :spawn,  :abort,  :syscall,  :open,  :printf,  :print,  :putc,  :instance_method,  :public_instance_method,  :<=>,  :define_method,  :<=,  :>=,  :==,  :===,  :included_modules,  :include?,  :ancestors,  :attr,  :attr_reader,  :attr_writer,  :attr_accessor,  :instance_methods,  :public_instance_methods,  :protected_instance_methods,  :private_instance_methods,  :constants,  :freeze,  :inspect,  :const_set,  :const_get,  :const_source_location,  :const_defined?,  :class_variable_set,  :class_variables,  :remove_class_variable,  :class_variable_get,  :const_missing,  :class_variable_defined?,  :<,  :private_constant,  :>,  :singleton_class?,  :public_constant,  :deprecate_constant,  :prepend,  :include,  :module_exec,  :to_s,  :module_eval,  :class_exec,  :class_eval,  :pretty_print_cycle,  :remove_method,  :undef_method,  :alias_method,  :method_defined?,  :public_method_defined?,  :private_method_defined?,  :name,  :protected_method_defined?,  :public_class_method,  :private_class_method,  :pretty_print,  :pretty_print_instance_variables,  :pretty_print_inspect,  :singleton_class,  :dup,  :itself,  :taint,  :tainted?,  :untaint,  :untrust,  :untrusted?,  :trust,  :methods,  :singleton_methods,  :protected_methods,  :private_methods,  :public_methods,  :instance_variables,  :instance_variable_get,  :instance_variable_set,  :instance_variable_defined?,  :remove_instance_variable,  :instance_of?,  :kind_of?,  :is_a?,  :display,  :hash,  :public_send,  :class,  :frozen?,  :tap,  :yield_self,  :then,  :extend,  :clone,  :method,  :public_method,  :singleton_method,  :define_singleton_method,  :=~,  :!~,  :nil?,  :eql?,  :respond_to?,  :object_id,  :send,  :to_enum,  :enum_for,  :pretty_inspect,  :__send__,  :!,  :instance_eval,  :instance_exec,  :!=,  :equal?,  :__id__]
{% endhighlight %}

Now you see an even longer list of methods than before. We can check the sizes of these lists:

{% highlight ruby linenos %}

BasicObject.methods.size # => 118
Kernel.methods.size # => 175

{% endhighlight %}

We can also see precisely what the methods that belong to `Kernel` but not to `BasicObject`:

{% highlight ruby %}
Kernel.methods - BasicObject.methods
# => [:puts,  :readline,  :readlines,  :p,  :Complex,  :Float,  :caller,  :caller_locations,  :set_trace_func,  :sprintf,  :format,  :Integer,  :String,  :Array,  :Hash,  :local_variables,  :fork,  :exit,  :pp,  :Pathname,  :warn,  :test,  :raise,  :gets,  :fail,  :global_variables,  :__method__,  :__callee__,  :__dir__,  :proc,  :lambda,  :eval,  :iterator?,  :block_given?,  :catch,  :throw,  :loop,  :sleep,  :rand,  :srand,  :trap,  :select,  :`,  :trace_var,  :untrace_var,  :load,  :at_exit,  :require_relative,  :require,  :binding,  :Rational,  :exec,  :exit!,  :system,  :spawn,  :abort,  :syscall,  :open,  :printf,  :print,  :putc]
{% endhighlight %}

But wait:

{% highlight ruby %}

Object.methods.size # => 118

{% endhighlight %}

What is going on here? Shouldn't `Object` have at least 175 methods like `Kernel`? Actually, no. Ruby does not support multiple inheritance. One of the methods defined in `BasicObject` is `:superclass`. Obviously, `BasicObject` does not have a superclass (parent class):

{% highlight ruby %}
BasicObject.superclass # => nil
{% endhighlight %}

But `Object` has:

{% highlight ruby %}
Object.superclass # => BasicObject
{% endhighlight %}

So `Object` inherits from `BasicObject`. We can even check the following:

{% highlight ruby %}
Object.methods == BasicObject.methods # => true
{% endhighlight %}

What is `PP::ObjectMixin` and `Kernel` doing "above" `Object` when we look at the ancestors of `MySimpleClass`? Simple: as we saw before, `PP::ObjectMixin` and `Kernel` are modules, not classes, and we know that a class can only have one superclass. While we can create a class that inherits from another class (and just one), we can `include` as many modules we want using the principle of <em>composition</em>. Therefore we can check:

{% highlight ruby %}
Object.included_modules
# => [PP::ObjectMixin, Kernel]
MySimpleClass.included_modules
# => [PP::ObjectMixin, Kernel]
{% endhighlight %}

When I created `MySimpleClass`, it automatically inherited from `Object`, which includes the modules `PP::ObjectMixin` and `Kernel`, and therefore `MySimpleClass` also include these modules. The correct way to read the ancestors list is as follows: `MySimpleClass` inherits from `Object`, `Object` includes `PP::ObjectMixin` and `Kernel`, and `Object` inherits from `BasicObject`.

How are modules included in a class? Consider the following module:

{% highlight ruby linenos %}
module MyModule
  def my_first_method
    puts "My first method"
  end

  def my_second_method
    puts "My second method"
  end
end
{% endhighlight %}

Now we create `MySimpleClass` and include `MyModule` as follows:

{% highlight ruby linenos %}
class MySimpleClass
  include MyModule

  def some_method
    puts "Some method"
  end
end
{% endhighlight %}

And we can check what instance methods we have now available in `MySimpleClass`:

{% highlight ruby %}
MySimpleClass.instance_methods - Object.methods
# => [:some_method, :my_first_method, :my_second_method]
{% endhighlight %}

In ruby, the top-level object (something similar to the scope of main in C) is `Object` and we know `Object` includes `Kernel`. Therefore, the methods defined in Kernel are available to `Object` and any of its descendants without the need to reference to Kernel explicitly! This includes methods we use without even thinking such as `puts`, `rand`, `raise`, `catch`, `throw`, and all the others defined in `Kernel`. This is why we can execute:

{% highlight ruby %}
puts "Hello!"
# => Hello!
{% endhighlight %}

instead of

{% highlight ruby %}
Kernel.puts "Hello!"
# => Hello!
{% endhighlight %}

In fact, when you are running IRB (interactive Ruby), a REPL (Read-Eval-Print-Loop) environment, and you type

{% highlight ruby %}
self
# => main
{% endhighlight %}

you get `main`. We never define `main` in Ruby. This is just to indicate that the top-level object in Ruby (a language where everything is an object), is an instance of `Object`:

{% highlight ruby %}
self.class
# => Object
{% endhighlight %}

## Manipulating Language Constructs

Let's take a look at how Ruby allows us to interact with its "vibrant citizens", as Perrotta describes Ruby's language constructs. When you hear that Ruby is a dynamic language, you should know that Ruby is pretty serious about that.

As an example, consider the following class:

{% highlight ruby linenos %}
class AnotherClass
  attr_reader :full_name, :dob
  attr_accessor :email, :phone, :zipcode

  SEPARATOR = "-"

  def initialize(first_name, last_name, dob, email, phone, zipcode)
    @first_name = first_name
    @last_name = last_name
    @email = email
    @dob = parse_date(dob)
    @phone = parse_phone(phone)
    @zipcode = zipcode
  end

  def full_name
    [first_name, last_name].join(", ")
  end

  def contact
    "#{full_name}
    #{email}
    #{phone}
    #{zipcode}"
  end

  private

  def parse_date(date)
    [4,7].each{|i| date.insert(i,SEPARATOR)}
    date
  end

  def parse_phone(phone)
    [3,7].each{|i| phone.insert(i,SEPARATOR)}
    phone
  end
end
{% endhighlight %}

We can now interact with Ruby's vibrant citizens in a number of ways. First, we instantiate an object of `AnotherClass`:

{% highlight ruby linenos %}
obj = AnotherClass.new "John", "Smith", "19950223", "jsmith@domain.com", "8205550123", "501234"
# => #<AnotherClass:0x000000010fa44b50, @first_name="John", @last_name="Smith", @dob="1995-02-23", @email="jsmith@domain.com", @phone="820-555-0123", @zipcode="501234">
{% endhighlight %}

Then we information from `obj` and `AnotherClass` such as:

{% highlight ruby linenos %}
obj.class
# => AnotherClass
obj.class.ancestors
# => [AnotherClass, Object, PP::ObjectMixin, Kernel, BasicObject]
obj.instance_variables
# => [:@first_name, :@last_name, :@email, :@dob, :@phone, :@zipcode]
obj.public_methods - Object.public_methods
# => [:full_name, :contact, :phone=, :zipcode=, :email, :email=, :dob, :phone, :zipcode]
obj.private_methods - Object.private_methods
# => [:name, :parse_date, :parse_phone, :contact_full_name, :autoload?, :autoload]
# We can list the parameters of any given method, if any
[:parse_date, :parse_phone].map{|m| obj.method(m).parameters.map{|params| {method: m, params: params[1..-1]}}.flatten}
# => [[{:method=>:parse_date, :params=>[:date]}], [{:method=>:parse_phone, :params=>[:phone]}]]
AnotherClass.constants
# => [:SEPARATOR]
AnotherClass.name
# => "AnotherClass"
# We can infer what methods are setters
(AnotherClass.instance_methods - Object.instance_methods).select{|m| m.to_s.include?("=")}
# => [:phone=, :zipcode=, :email=]
{% endhighlight %}

The above is far from exhaustive. It is certainty good to be able to dynamically interact with the language constructs in Ruby. How we do it is even better.

# Dynamic Dispatch

Dynamic Dispatch is a technique that allows us to treat a method name as an argument that can be passed to another method that handles its execution. When we create instance methods for any given class in Ruby, we typically call them using the dot notation. As an example, consider the code below:

{% highlight ruby linenos %}

class MySimpleClass
  def my_simple_method(string1, string2)
    string1 + " " + string2
  end
end

obj = MySimpleClass.new
obj.my_simple_method("Hello","World") # => "Hello World"

{% endhighlight %}

Alternatively, we obtain the same result using the method `:send` as follows:

{% highlight ruby %}

obj.send(:my_simple_method,"Hello","World") # => "Hello World"

{% endhighlight %}

How dynamic is Ruby? Let's say that the last definition of `MySimpleClass` was the very first we created. If in a future moment I do:

{% highlight ruby linenos %}
class MySimpleClass
  def some_other_method
    puts "Some other method"
  end
end
{% endhighlight %}

and I request the instance methods of `MySimpleClass`, I obtain:

{% highlight ruby %}
MySimpleClass.instance_methods - Object.methods
# => [:some_method, :some_other_method, :my_first_method, :my_second_method]
{% endhighlight %}

And if we consider the very first definition of `MySimpleClass` is still accessible in memory, then we obtain:

{% highlight ruby %}
MySimpleClass.instance_methods - Object.methods
# => [:my_simple_method, :some_method, :some_other_method, :my_first_method, :my_second_method]
{% endhighlight %}

Therefore we can modify the struct of a class at the time of the execution of a program.

To show one form of Dynamic Dispatch in action, consider the following class:

{% highlight ruby linenos %}
class AnotherSimpleClass
  def first_method_with_no_arguments
    puts "First method with no arguments"
  end

  def second_method_with_no_arguments
    puts "Second method with no arguments"
  end

  def third_method_with_no_arguments
    puts "Third method with no arguments"
  end

  def first_method_with_two_arguments(string1, string2)
    puts string1 + " " + string2
  end

  def second_method_with_two_argumetns(string1, string2)
    puts string1 + " => " + string2
  end
end
{% endhighlight %}

For any instance method in `AnotherSimpleClass`, we their argumetns:

{% highlight ruby linenos %}
obj = AnotherSimpleClass.new
obj.method(:first_method_with_no_arguments).parameters # => []
obj.method(:first_method_with_two_arguments).parameters # => [[:req, :string1], [:req, :string2]]
{% endhighlight %}

Therefore we can manipulate these language constructs for dynamically calling these methods. Let's say that I want to call all methods with no arguments. I can do the following:

{% highlight ruby linenos %}
methods_no_arguments = (obj.methods - Object.methods).select{|m| obj.method(m).parameters.empty?}
# => [:first_method_with_no_arguments, :second_method_with_no_arguments, :third_method_with_no_arguments]
methods_no_arguments.each{|m| obj.send(m) }
# => First method with no arguments
# => Second method with no arguments
# => Third method with no arguments
{% endhighlight %}

For invoking only the methods with two arguments, I proceed as follows:

{% highlight ruby linenos %}
methods_two_arguments = (obj.methods - Object.methods).select{|m| obj.method(m).parameters.size == 2}
# => [:first_method_with_two_arguments, :second_method_with_two_argumetns]
methods_two_arguments.each{|m| obj.send(m, "Hello", "World")}
# => Hello World
# => Hello => World
{% endhighlight %}

The method `send` will call any method in the respective class, including private methods. If you want to confine the dynamic execution of methods to public methods you can use `public_send` instead.

# Dynamic Methods

We already saw that we can add methods to an existing class as if we were creating the class for the first time. But there is a shorter way to do that. Consider our existing `AnotherSimpleClass`. We can dynamically define a new method as follows:

{% highlight ruby linenos %}
AnotherSimpleClass.define_method :my_new_method do |arg1, arg2|
  puts "arg1 = #{arg1}"
  puts "arg2 = #{arg2}"
end

obj = AnotherSimpleClass.new
obj.my_new_method("Hello", [1,2,3,4])
# => arg1 = Hello
# => arg2 = [1, 2, 3, 4]

obj.methods - Object.methods
=> [:first_method_with_two_arguments, :second_method_with_two_argumetns, :my_new_method, :first_method_with_no_arguments, :second_method_with_no_arguments, :third_method_with_no_arguments]
{% endhighlight %}

When it comes to metaprogramming, the advantage of using `define_method` instead of `def method` is that we can pass the name of the new method as an argument, which can be done at runtime.

# Ghost Methods

What happens when we call a method in Ruby? Consider the the instance `obj = AnotherSimpleClass`. When we call the method `:first_method_with_no_arguments`, Ruby looks at `obj.instance_methods` trying to find that method. If it finds it, it will call it. If it does not find it, then it will try to look for an implementation of a private method in `BasicObject` called `:method_missing`:

{% highlight ruby linenos %}
BasicObject.private_methods.size # => 87
BasicObject.private_methods.select{|m| m.to_s.include?("missing")}
# => [:respond_to_missing?, :method_missing]
{% endhighlight %}

If Ruby does not find an implementation for `:method_missing` (I will talk about this later) then it calls `:method_undefined`. Let's see this in practice:

{% highlight ruby linenos %}
obj = AnotherSimpleClass.new
obj.crazy
# => NoMethodError (undefined method `crazy' for #<AnotherSimpleClass:0x00007fe3be15db60>)
{% endhighlight %}

We can't call `method_undefined` using dot notation since it is a private method, as we can see here:

{% highlight ruby %}
BasicObject.private_methods.select{|m| m.to_s.include?("undefined")}
# => [:method_undefined, :singleton_method_undefined]
{% endhighlight %}

But we call it using `:send`:

{% highlight ruby %}
obj.send(:method_undefined, :crazy)
# => NoMethodError (undefined method `method_undefined' for #<AnotherSimpleClass:0x00007fe3be15db60>)
{% endhighlight %}

Ok, so we know how Ruby call methods and what happens when it cannot find them. But what does it mean to loo for an implementation for `:method_missing`?

## Method Missing

In Ruby, there is no such thing as a compiler to enforce method calls. Crazy, right? Even crazier is the fact that Ruby allows you to call methods that don't exist! Let me give you one example of how useful this can be. I will create a data set called `data`:

{% highlight ruby linenos %}
data = []
data << {name: "John", age: 25, gender: "M", state: "CO"
data << {name: "Mary", age: 23, gender: "F", state: "CO"}
data << {name: "Gloria", age: 20, gender: "F", state: "FL"}
data << {name: "Paul", age: 23, gender: "M", state: "CA"}
data << {name: "Barb", age: 26, gender: "F", state: "TX"}
data << {name: "Jerry", age: 29, gender: "M", state: "TX"}
# => [{:name=>"John", :age=>25, :gender=>"M", :state=>"CO"}, {:name=>"Mary", :age=>23, :gender=>"F", :state=>"CO"}, {:name=>"Gloria", :age=>20, :gender=>"F", :state=>"FL"}, {:name=>"Paul", :age=>23, :gender=>"M", :state=>"CA"}, {:name=>"Barb", :age=>26, :gender=>"F", :state=>"TX"}, {:name=>"Jerry", :age=>29, :gender=>"M", :state=>"TX"}]
{% endhighlight %}

I will now create a class `MyDatabase` and initialize it passing `data` as an argument. I will also create an implementation for `:method_missing` so we can take advantage of the dynamically creating and calling methods in Ruby:

{% highlight ruby linenos %}
class MyDatabase
  attr_reader :data

  def initialize data
    @data = data
  end

  def method_missing(m, *args)
    # I am looking for a pattern like part1_part2_part3 or part1_part2_part3_part4
    # the method split takes some character or string as a separator and creates an array
    parts = m.to_s.split("_")

    # In the first condition:
    # Check if there are three parts
    # Check if all the parts have content
    # Check if the array of hashes includes the informed key

    # In the second condition is similar to the first except this time:
    # Check if there are four parts
    if parts.size == 3 && parts.map{|a| !a.empty? }.uniq == [true] &&
       data[0].keys.include?(parts[2].to_sym)

      data.send(parts[0].to_sym){|d| d[parts[2].to_sym] == args[0]}
      elsif parts.size == 4 && parts.map{|a| !a.empty? }.uniq == [true] &&
        data[0].keys.include?(parts[3].to_sym)

      data.send(parts[0..1].join("_")){|d| d[parts[3].to_sym] == args[0]}
    else
      # if the conditions I specified are not met, I pass control to the
      # original implementation of method_missing, which will not find
      # the method and will call :method_undefined
      super
    end
  end
end
{% endhighlight %}

We can instantiate `MyDatabase` passing the array `data` as argument:

{% highlight ruby linenos %}
db = MyDatabase.new data
db.data
# => [{:name=>"John", :age=>25, :gender=>"M", :state=>"CO"}, {:name=>"Mary", :age=>23, :gender=>"F", :state=>"CO"}, {:name=>"Gloria", :age=>20, :gender=>"F", :state=>"FL"}, {:name=>"Paul", :age=>23, :gender=>"M", :state=>"CA"}, {:name=>"Barb", :age=>26, :gender=>"F", :state=>"TX"}, {:name=>"Jerry", :age=>29, :gender=>"M", :state=>"TX"}]
{% endhighlight %}

We can now do:

{% highlight ruby linenos %}
db.find_by_name("Gloria")
# => {:name=>"Gloria", :age=>20, :gender=>"F", :state=>"FL"}
db.find_by_state("TX")
# => {:name=>"Barb", :age=>26, :gender=>"F", :state=>"TX"}
db.find_all_by_age(23)
# => [{:name=>"Mary", :age=>23, :gender=>"F", :state=>"CO"}, {:name=>"Paul", :age=>23, :gender=>"M", :state=>"CA"}]
db.find_all_by_gender("F")
# => [{:name=>"Mary", :age=>23, :gender=>"F", :state=>"CO"}, {:name=>"Gloria", :age=>20, :gender=>"F", :state=>"FL"}, {:name=>"Barb", :age=>26, :gender=>"F", :state=>"TX"}]
db.find_by_country("US")
# => NoMethodError (undefined method `find_by_country' for #<MyDatabase:0x00007fc14e91b3d0>)
db.find_all_by_weight(180)
# => NoMethodError (undefined method `find_all_by_weight' for #<MyDatabase:0x00007fc14e91b3d0>)
{% endhighlight %}

This is just a toy example to show what kind of features one can build by dynamically manipulating language constructs in Ruby. Perhaps the most prominent example of dynamic method execution via implementations of `:method_missing` is the <a href="https://guides.rubyonrails.org/active_record_basics.html">ActiveRecord</a>, an object-relational mapping in Rails. Here is one <a href="https://github.com/rails/rails/blob/main/activerecord/lib/active_record/dynamic_matchers.rb">example</a>.

# Dynamic Proxy

In previous example with `MyDatabase`, I receive whatever is passed on via method call and try to make sense of the call using pre-defined patterns. If the conditions specified are met, a method is dynamically called and the associated result is returned. A similar approach is known as Dynamic Proxy. We still use the idea of Ghost Methods but this time we forward the call to another method (which can be in another module or class). The greatest difference between Ghost Method and Dynamic Proxy is how to deal with responsibility. When working with Ghost Method in a particular class, you have the responsibility of implementing `:method_missing` and deciding when and how to give up and let Ruby call `:method_undefined`. With Dynamic Proxy, you forward the responsibility to another method and let it treat it each situation according whatever rules are in place.

Here is an example: we have class `Person` and we want to "monitor" any call to a method `:parse` but we don't want implement the logic. Instead, we will forward the logic to `JSON.parse`. So whatever rule `JSON` implemented for `:parse` will take place only when the method `:parse` is called for an instance of `Person`.

{% highlight ruby linenos %}
require 'json'

class Person
  attr_accessor :name, :age

  def method_missing(m, *args)
    if m == :parse
      data = JSON.parse(args[0])
      if data.keys.map{|d| self.respond_to? d}.uniq == [true]
        self.name = data["name"]
        self.age = data["age"]
        self.to_s
      end
    else
      super
    end
  end

  def to_s
    "Person => name: #{name}, age: #{age}"
  end
end
{% endhighlight %}

We can now call `:parse` in Person:

{% highlight ruby linenos %}
person.parse('{"name": "John", "age": "25"}')
# Person => name: John, age: 25
{% endhighlight %}

However, when we try to parse a different string, we obtain an error:

{% highlight ruby linenos %}
person.parse('{"name" => "John", "age" => "25"}')
# => unexpected token at '{"name" => "John", "age" => "25"}' (JSON::ParserError)
{% endhighlight %}

And that decision was made by the `JSON`'s implementation of `:parse`.

If we try something different than parse and it is a method that is not present in the list of methods of `Person`'s ancestors, then we obtain the default behavior for undefined methods:

{% highlight ruby linenos %}
person.infuse('{"name" => "John", "age" => "25"}')
# => undefined method `infuse' for #<Person:0x00007f9ba818a380>
{% endhighlight %}

# Blank Slate

Now let's assume that for some reason I thought that it was a great idea to implement a Dynamic Proxy for any method call starting with "display" for a new class called `MyNewClass`. My goal is to return just the object ID. So I create `MyNewClass` as follows:

{% highlight ruby linenos %}
class MyNewClass

  def method_missing(m, *args)
    if m.to_s.include?("display")
      "Object ID: #{self.__id__}"
    else
      super
    end
  end
end
{% endhighlight %}

So I try calling the method `:display_info`:

{% highlight ruby linenos %}
obj = MyNewClass.new
obj.display_info
# => Object ID: 70138108065880
{% endhighlight %}

Everything seems to be working nicely, but not quite. When I try calling just `:display`, the following happens:

{% highlight ruby %}
obj.display
# => #<MyNewClass:0x00007ffdce0165e8>
{% endhighlight %}

This is not what I was expecting. This happens because `MyNewClass`'s parent class is `Object`, and `Object` implements an instance method `:display`. Therefore, when I call `:display`, Ruby looks for a method `:display` in the list of methods, including `Object`. Ruby will find `Object`'s implementation of `:display` which just prints the bare object and returns `nil`. This is a simple example of a problem that can occur very frequently when using Dynamic Proxy, specially in larger projects: the name of a "Ghost Method" can match the name of an existing method that belong to one of the object's class ancestors.

Most of the time, we need a fully featured object with all the methods defined in `Object`. Some other times, we need some simpler. As class with a minimum number of methods is referred to as Blank Slate. One way to solve our problem is to modify `MyNewClass` to inherit from `BasicObject` instead of implicitly inheriting from `Object`.

The class `Object` has 58 instance methods:

{% highlight ruby %}
Object.instance_methods
# => [:instance_variable_defined?, :remove_instance_variable, :instance_of?, :kind_of?, :is_a?, :tap, :instance_variable_get, :instance_variable_set, :instance_variables, :singleton_method, :method, :public_send, :define_singleton_method, :public_method, :extend, :to_enum, :enum_for, :<=>, :===, :=~, :!~, :eql?, :respond_to?, :freeze, :inspect, :object_id, :send, :to_s, :display, :nil?, :hash, :class, :singleton_class, :clone, :dup, :itself, :yield_self, :then, :taint, :tainted?, :untaint, :untrust, :untrusted?, :trust, :frozen?, :methods, :singleton_methods, :protected_methods, :private_methods, :public_methods, :equal?, :!, :__id__, :==, :instance_exec, :!=, :instance_eval, :__send__]
{% endhighlight %}

The class `BasicObject` has only 8 instance methods:

{% highlight ruby %}
BasicObject.instance_methods
# => [:equal?, :!, :__id__, :==, :instance_exec, :!=, :instance_eval, :__send__]
{% endhighlight %}

More importantly, `BasicObject` does not implement `:display`. So we can modify `MyNewClass` as follows:

{% highlight ruby lineos %}
class MyNewClass < BasicObject

  def method_missing(m, *args)
    if m.to_s.include?("display")
      "Object ID: #{self.__id__}"
    else
      super
    end
  end
end
{% endhighlight %}

`MyNewClass` now inherits from `BasicObject`, which makes `MyNewClass` a Blank Slate. Of course, we lose most of the functionalities we would need for a more comprehensive class, including all functionality given by the `Kernel`. But for the sake of this illustration, with the modification above, we can now call all variations of "display", including `:display`, and we will obtain the expected result:

{% highlight ruby linenos %}
obj = MyNewClass.new
puts obj.display
# => Object ID: 70183204985940
{% endhighlight %}

# Code that Writes Code

I told you before that I don't like the "code that writes code" definition of metaprogramming but that doesn't mean we can't have fun with it. Here is a simple example on how create classes and instantiate object for theses classes dynamically. Imagine that I have a file name `person.csv` with the following content:

{% highlight csv %}
name,age,gender,state
John,25,M,CO
Mary,23,F,CO
Gloria,20,F,FL
Paul,23,M,CA
Barb,26,F,TX
Jerry,29,M,TX
{% endhighlight %}

I will write a code that will read the content of `person.csv`, create a class `Person` and define its attributes based on the first line of the file and then instantiate objects of `Person` with the data in the remainder of the file. In fact, the code will work for any csv file following the same pattern, that is, the first line contains the attribute names and the remainder of the file contains data associated with those attributes.

{% highlight ruby linenos %}
# process_csv.rb

files = Dir["*.csv"]

database = []

files.each do |filename|
  class_name = filename.split(".")[0].capitalize
  lines = File.readlines(filename)
  $attributes = lines[0].strip.split(",").map(&:to_sym)
  data = lines[1..-1].map{|d| d.strip.split(",")}

  new_class = Class.new(Object) do
    attr_accessor *$attributes

    def initialize(*args)
      $attributes.zip(args) do |attribute, value|
        instance_variable_set("@#{attribute.to_s}", value)
      end
    end
  end

  my_class = Object.const_set(class_name, new_class)

  collection = data.map do |d|
    new_class.new(*d)
  end
  database << {class: new_class, data: collection}
end

database.each do |db|
  puts db[:class]
  puts "==============================================================================="
  db[:data].each do |row|
    puts row.inspect
  end
  puts ""
end

{% endhighlight %}

# Refactoring with Metaprogramming

Now that we saw a little bit of the very basics of metaprogramming in Ruby, let's review a very interesting example Perrotta discuss in his book. Imagine that you are analyzing a very strange legacy Ruby code full of duplications. Your task is to improve it as much as possible. You receive two files: `data_source.rb` and `duplicated.rb`. The `data_source.file` is partially showed below:

{% highlight ruby linenos %}
# data_source.rb

class DS
  def initialize # ...
  def get_cpu_info(workstation_id) # ...
  def get_cpu_price(workstation_id) # ...
  def get_mouse_info(workstation_id) # ...
  def get_mouse_info(workstation_id) # ...
  def get_keyboard_info(workstation_id) # ...
  def get_keyboard_price(workstation_id) # ...
  # ... etc
end
{% endhighlight %}

The exact logic of `DS` is supressed in the display. Just assume that when you pass a `workstation_id` as argument to one of the methods in `DS`, `DS` will connect to a database and return the required information:

{% highlight ruby linenos %}
ds = DS.new
ds.get_cpu_info(42)     # => "2.9 Ghz quad-core"
ds.get_cpu_price(42)    # => 120
ds.get_mouse_info(42)   # => "Wireless Touch"
ds.get_mouse_price(42)  # => 60
{% endhighlight %}

And here `duplicated.rb` (slightly modified for simplicity):

{% highlight ruby linenos %}
class Computer
  def initialize(computer_id, data_source)
    @id = computer_id
    @data_source = data_source
  end

  def cpu
    info = @data_source.get_cpu_info(@id)
    price = @data_source.get_cpu_price(@id)
    "CPU: #{info} ($#{price})"
  end

  def mouse
    info = @data_source.get_mouse_info(@id)
    price = @data_source.get_mouse_price(@id)
    "Mouse: #{info} ($#{price})"
  end

  def keyboard
    info = @data_source.get_keyboard_info(@id)
    price = @data_source.get_keyboard_price(@id)
    "Keyboard: #{info} ($#{price})"
  end

  # ...
end
{% endhighlight %}

You know where this is going, right? You can now identify the duplications and how we can use the strategies we previously discussed to improve this code. First, we can use Dynamic Methods and Dynamic Dispatch:

{% highlight ruby linenos %}
class Computer
  def initialize(computer_id, data_source)
    @id = computer_id
    @data_source = data_source
    data_source.methods.grep(/^get_(.*)_info$/) { Computer.define_component $1 }
  end

  # Added explanation:
  # Notice that we just need the name of the resource
  # so it suffices to get the name from get_*_info methods since get_*_price
  # repeats the name of the resource.
  # The $1 is just a global variable that words as a type of placeholder for
  # a later use.

  def self.define_component(name)
    define_method(name) do
      info = @data_source.send "get_#{name}_info", @id
      price = @data_source.send "get_#{name}_price", @id
      "#{name.capitalize}: #{info} ($#{price})"
    end
  end
end
{% endhighlight %}

Second, we can use Ghost Methods, and a Dynamic Proxy that is also a Blank Slate:

{% highlight ruby linenos %}
class Computer < BasicObject
  def initialize(computer_id, data_source)
    @id = computer_id
    @data_source = data_source
  end

  def method_missing(name, *args)
    super if !@data_source.respond_to?("get_#{name}_info")
    info = @data_source.send "get_#{name}_info", @id
    price = @data_source.send "get_#{name}_price", @id
    "#{name.capitalize}: #{info} ($#{price})"
  end
end
{% endhighlight %}

And so, we used all four strategies for metaprogramming in Ruby that we discussed in this post. Notice the method `:respond_to?` in the `Computer`'s implementation of `:method_missing`. When an object calls `:respond_to?`, Ruby will respond if that object implements the method passed as argument. You could asked: "But isn't the idea of `:method_missing` to dynamically implement a method that does not exist?" Correct. However, we are implementing a method in `Computer` and we are checking if the method in question exists in `DS`. We need that method to exist in `DS` to make this logic work, therefore we fist check if the method exists in `DS` and if not, we call the original implementation of `:method_missing`. Otherwise, we continue with our implementation.

# There is More

In this post I briefly discussed metaprogramming strategies with Ruby such as Dynamic Dispatch, Dynamic Methods, Ghost Methods, Dynamic Proxy, and Blank Slate. Paolo Perrotta refers to these strategies as "spells". In his book, many other spells are discussed: Around Alias, Class Extension, Class Instance Variable, Class Macro, Clean Room, Code Processor, Deferred Evaluation, Flat Scope, Hook Method, Kernel Method, Lazy Instance Method, Mimic Method, Monkey Patch, Namespace, Nil Guard, Object Extension, Open Class, Prepend Wrapper, Refinement, Refinement Wrapper, Sandbox, Scope Gate, Self Yield, Shared Scope, Singleton Method, String of Code, and Symbol to Proc. Trust me: I didn't even scratch the surface. There is much more to metaprogramming in Ruby.

# Conclusions

Ruby is a dynamic language by design. Not only its syntax is concise and elegant but also its constructs are available for meaningful manipulations which takes object-oriented programming to its full potential and makes metaprogramming in Ruby a delightful experience. For this reason I find Ruby the best language for prototyping I know. 
