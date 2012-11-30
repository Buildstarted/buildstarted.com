---
layout: default
title: Entity framework with many to many one to many and one to one relationships
---

<h2>Child/Parent object assignments</h2>

<p>One of the more common things you're going to do in <strong>Entity Framework</strong> is link two objects together. In it's simplest form you just have to create a virtual property of the object in question within the consuming object. One of the nice things about Entity Framework is that it can handle all the mapping for you. Yay for us. </p>

<h2>Many-To-Many</h2>

<pre><code>public class DC : DbContext {
    public DbSet<Student> Students { get; set; }
    public DbSet<Class> Classes { get; set; }
}

public class Student {
    public int Id { get; set; }
    public string Name { get; set; }
    public virtual ICollection<Class> Classes { get; set; }
}

public class Class {
    public int Id { get; set; }
    public string Name { get; set; }
    public virtual ICollection<Student> Student { get; set; }
}
</code></pre>

<p>Our database is setup with the following tables. Students, Classes and Classes&#95;Student for the many-to-many mapping. </p>

<p>Here we have a student that with a list of classes and a class with a list of students. **john** doesn't have any classes assigned to him and likewise **Rhyming 101** has no students, yet. To add a student to a class or a class to a student we can do the following. (Assuming we have data in the database)</p>

<pre><code>rhyming101 = dc.Classes.Single(c => c.Id == 1);
john = dc.Students.Single(s => s.Id == 1);

john.Classes.Add(rhyming101);
//or
rhyming101.Students.Add(john);
</code></pre>

<p>However, suppose we're creating the student and we'd like as few trips the data as possible. I would attempt the following.</p>

<pre><code>DC dc = new DC();
Student john = new Student() {
   Name = "Smith",
   Classes = new List<Class>() { new Class() { Id == 1 } }
};
</code></pre>

<p>The above code actually creates a new Class in the data ignoring the passed Id entirely. Which, to me, seems to be a mistake but it's what we're stuck with. We could make a call to the dc here and just grab the class by Id but that seems to be an unnecessary trip to the data.</p>

<pre><code>DC dc = new DC();
Student john = new Student() {
   Name = "Smith",
   Classes = new List<Class>() { dc.Classes.Single(c => c.Id == 1) }
};
</code></pre>

<p>I'm not really sure any better way to handle this. Hopefully the comments will show a way to do it. (hint, hint)</p>

<h2>One-To-Many</h2>

<p>Teachers and Classes. In this example each teacher one class is taught by one teacher but a teacher may teach many classes (to make my life easy). So we need to setup a <strong>one-to-many</strong> relationship. We're going to modify our Class and create a new Teacher class.</p>

<pre><code>public class Class {
    public int Id { get; set; }
    public string Name { get; set; }
    public virtual ICollection<Student> Student { get; set; }
    public virtual Teacher { get; set; {
}

public class Teacher {
    public int Id { get; set; }
    public string Name { get; set; }
    public virtual ICollection<Class> Classes { get; set; }
}
</code></pre>

<p>Our Classes table in the database now includes an additional Teacher&#95;Id for us to map to the appropriate Teacher. Awesome, we don't have to keep track of it and we dont' have to include it in our model.</p>

<p>We now have a Teacher which has a collection of Classes assigned to them. There are two ways to make the assignments. One is to assign the class to the teacher by adding it to the classes and the other is to assign the teacher to the class.</p>

<pre><code>DC dc = new DC();
Teacher teacher = dc.Teachers.Single(t => t.Id == 1);
Class @class = dc.Classes.Single(c => c.Id == 1);

teacher.Classes.Add(@class);
//or
@class.Teacher = teacher;
</code></pre>

<h2>One-To-One</h2>

<p>Now, let's modify our Student and add a Locker. To Add a locker to a student you could do it one of two ways. Assign the student to the locker or assign the locker to the student. Our Student table now includes a foreign key to Locker&#95;Id automatically for us, in addition to the Student&#95;Id in the Locker table. Remember, this is all done behind the scenes and it isn't necessary to add these fields to your POCO.</p>

<pre><code>public class Student {
    public int Id { get; set; }
    public string Name { get; set; }
    public virtual ICollection<Class> Classes { get; set; }
    public virtual Locker Locker { get; set; }
}

public class Locker {
    public int Id { get; set; }
    public string SecretCode { get; set; }
    public virtual Student Student { get; set; }
}

Student john = new Student() {
    Name = "John"
}

Locker CA592B = new Locker() {
    Secret = "CA592B"
}

john.Locker = CA592B;
//or
CA592B.Student = john;
</code></pre>

<h2>Final</h2>

<p>Our database structure ends up with several tables, <strong>Classes</strong>, <strong>Classes&#95;Student</strong>, <strong>Lockers</strong>, <strong>Students</strong>, and <strong>Teachers</strong>. Each one has appropriate foreign keys and the Classes&#95;Students table isn't required to be created in POCO. This saves us a lot of hassle. Don't forget. Every virtual property type must have an equivalent **DbSet</strong> in the DataContext. </p>

<p>I hope this helps anyone understand how <strong>Entity Framework</strong> works a bit more.</p>

<h3>- Ben</h3>