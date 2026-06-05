# FORMS 

```html
<!DOCTYPE html>
<html>
<head>
    <title>Student Registration Form</title>
</head>
<body>

<h1>Student Registration Form</h1>

<form action="" method="post" autocomplete="on">

    <fieldset>

        <legend>Personal Details</legend>

        <label for="fname">Full Name :</label>
        <input
            type="text"
            id="fname"
            name="fname"
            placeholder="Enter Full Name"
            required
            minlength="3"
            maxlength="30"
            autofocus>
        <br><br>

        <label for="email">Email :</label>
        <input
            type="email"
            id="email"
            name="email"
            placeholder="Enter Email"
            required>
        <br><br>

        <label for="password">Password :</label>
        <input
            type="password"
            id="password"
            name="password"
            minlength="8"
            maxlength="20"
            required>
        <br><br>

        <label for="phone">Phone :</label>
        <input
            type="tel"
            id="phone"
            name="phone"
            placeholder="9876543210"
            required>
        <br><br>

        <label for="age">Age :</label>
        <input
            type="number"
            id="age"
            name="age"
            min="18"
            max="60"
            step="1">
        <br><br>

    </fieldset>

    <br>

    <fieldset>

        <legend>Gender</legend>

        <input type="radio" id="male" name="gender">
        <label for="male">Male</label>

        <input type="radio" id="female" name="gender">
        <label for="female">Female</label>

        <input type="radio" id="other" name="gender">
        <label for="other">Other</label>

    </fieldset>

    <br>

    <fieldset>

        <legend>Skills Using Select</legend>

        <label>Skills :</label>

        <select multiple>

            <optgroup label="Frontend">

                <option>HTML</option>
                <option>CSS</option>
                <option>JavaScript</option>

            </optgroup>

            <optgroup label="Backend">

                <option>Java</option>
                <option>Python</option>
                <option>NodeJS</option>

            </optgroup>

            <optgroup label="Database">

                <option>SQL</option>
                <option>MongoDB</option>

            </optgroup>

        </select>

    </fieldset>

    <br>

    <fieldset>

        <legend>Skills Using Datalist</legend>

        <label>Search Skill :</label>

        <input list="skillList">

        <datalist id="skillList">

            <option value="HTML">HTML</option>
            <option value="CSS">CSS</option>
            <option value="JavaScript">JavaScript</option>
            <option value="Java">Java</option>
            <option value="Python">Python</option>
            <option value="NodeJS">NodeJS</option>
            <option value="MongoDB">MongoDB</option>

        </datalist>

    </fieldset>

    <br>

    <fieldset>

        <legend>Course Information</legend>

        <label>Course Start Date :</label>
        <input type="date">
        <br><br>

        <label>Preferred Time :</label>
        <input type="time">
        <br><br>

        <label>Date and Time :</label>
        <input type="datetime-local">
        <br><br>

        <label>Week :</label>
        <input type="week">
        <br><br>

        <label>Month :</label>
        <input type="month">

    </fieldset>

    <br>

    <fieldset>

        <legend>Additional Details</legend>

        <label>Portfolio URL :</label>
        <input type="url">
        <br><br>

        <label>Search Course :</label>
        <input type="search">
        <br><br>

        <label>Favorite Color :</label>
        <input type="color">
        <br><br>

        <label>Upload Resume :</label>
        <input type="file">
        <br><br>

        <label>Confidence Level :</label>
        <input
            type="range"
            min="0"
            max="100">

    </fieldset>

    <br>

    <fieldset>

        <legend>Interests</legend>

        <input type="checkbox" id="coding">
        <label for="coding">Coding</label>

        <input type="checkbox" id="sports">
        <label for="sports">Sports</label>

        <input type="checkbox" id="music">
        <label for="music">Music</label>

    </fieldset>

    <br>

    <fieldset>

        <legend>Address</legend>

        <textarea
            rows="5"
            cols="40"
            placeholder="Enter Address">
        </textarea>

    </fieldset>

    <br>

    <fieldset>

        <legend>Readonly and Disabled Example</legend>

        <label>College Name :</label>
        <input
            type="text"
            value="GITAM University"
            readonly>
        <br><br>

        <label>Scholarship Status :</label>
        <input
            type="text"
            value="Not Available"
            disabled>

    </fieldset>

    <br>

    <button type="submit">
        Submit
    </button>

    <button type="reset">
        Reset
    </button>

</form>

</body>
</html>
```
