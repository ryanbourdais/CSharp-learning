# Project 7: Personal Website

## Assignment Summary
Create a simple personal website using ASP.NET Core MVC with multiple pages including About, Contact, and Portfolio sections. This project introduces web development concepts, HTML/CSS basics, and the MVC pattern.

## Learning Objectives
- Set up an ASP.NET Core MVC project
- Understand the MVC (Model-View-Controller) pattern
- Work with HTML and CSS basics
- Implement routing and navigation
- Create responsive web pages
- Deploy a web application

## Project Requirements

### Basic Requirements
1. **Project Setup**: Create ASP.NET Core MVC project
2. **Home Page**: Welcome page with introduction
3. **About Page**: Personal information and background
4. **Contact Page**: Contact form with validation
5. **Portfolio Page**: Show projects or work samples
6. **Navigation**: Consistent navigation between pages
7. **Styling**: Basic CSS for attractive appearance

### Advanced Requirements (Optional)
- Add a blog section
- Implement contact form functionality
- Add image galleries
- Create responsive design
- Add animations and effects
- Implement dark/light theme toggle

## Step-by-Step Instructions

### Step 1: Create Your Project
```bash
dotnet new mvc -n PersonalWebsite
cd PersonalWebsite
```

### Step 2: Understand the Project Structure
The MVC project creates this structure:
- `Controllers/` - Contains controller classes
- `Views/` - Contains HTML templates
- `Models/` - Contains data models
- `wwwroot/` - Contains static files (CSS, JS, images)

### Step 3: Create Controllers
Create controllers for each page:

```csharp
// Controllers/HomeController.cs
public class HomeController : Controller
{
    public IActionResult Index()
    {
        return View();
    }
}

// Controllers/AboutController.cs
public class AboutController : Controller
{
    public IActionResult Index()
    {
        return View();
    }
}

// Controllers/ContactController.cs
public class ContactController : Controller
{
    public IActionResult Index()
    {
        return View();
    }
}

// Controllers/PortfolioController.cs
public class PortfolioController : Controller
{
    public IActionResult Index()
    {
        return View();
    }
}
```

## Hints and Code Examples

### Basic Layout (_Layout.cshtml)
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>@ViewData["Title"] - My Personal Website</title>
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.min.css" />
    <link rel="stylesheet" href="~/css/site.css" />
</head>
<body>
    <header>
        <nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-light bg-white border-bottom box-shadow mb-3">
            <div class="container">
                <a class="navbar-brand" asp-controller="Home" asp-action="Index">My Website</a>
                <button class="navbar-toggler" type="button" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="navbar-toggler-icon"></span>
                </button>
                <div class="navbar-collapse collapse d-sm-inline-flex justify-content-between">
                    <ul class="navbar-nav flex-grow-1">
                        <li class="nav-item">
                            <a class="nav-link text-dark" asp-controller="Home" asp-action="Index">Home</a>
                        </li>
                        <li class="nav-item">
                            <a class="nav-link text-dark" asp-controller="About" asp-action="Index">About</a>
                        </li>
                        <li class="nav-item">
                            <a class="nav-link text-dark" asp-controller="Portfolio" asp-action="Index">Portfolio</a>
                        </li>
                        <li class="nav-item">
                            <a class="nav-link text-dark" asp-controller="Contact" asp-action="Index">Contact</a>
                        </li>
                    </ul>
                </div>
            </div>
        </nav>
    </header>

    <div class="container">
        <main role="main" class="pb-3">
            @RenderBody()
        </main>
    </div>

    <footer class="border-top footer text-muted">
        <div class="container">
            &copy; 2024 - My Personal Website
        </div>
    </footer>

    <script src="~/lib/jquery/dist/jquery.min.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
    <script src="~/js/site.js" asp-append-version="true"></script>
    @await RenderSectionAsync("Scripts", required: false)
</body>
</html>
```

### Home Page (Views/Home/Index.cshtml)
```html
@{
    ViewData["Title"] = "Home";
}

<div class="text-center">
    <h1 class="display-4">Welcome to My Personal Website</h1>
    <p class="lead">Hi, I'm [Your Name]. Welcome to my personal website!</p>
    
    <div class="row mt-5">
        <div class="col-md-4">
            <h3>About Me</h3>
            <p>I'm a passionate developer who loves creating amazing applications.</p>
            <a asp-controller="About" asp-action="Index" class="btn btn-primary">Learn More</a>
        </div>
        <div class="col-md-4">
            <h3>My Work</h3>
            <p>Check out some of my recent projects and achievements.</p>
            <a asp-controller="Portfolio" asp-action="Index" class="btn btn-primary">View Portfolio</a>
        </div>
        <div class="col-md-4">
            <h3>Get In Touch</h3>
            <p>Have a question or want to collaborate? Let's talk!</p>
            <a asp-controller="Contact" asp-action="Index" class="btn btn-primary">Contact Me</a>
        </div>
    </div>
</div>
```

### About Page (Views/About/Index.cshtml)
```html
@{
    ViewData["Title"] = "About";
}

<div class="row">
    <div class="col-md-8">
        <h1>About Me</h1>
        <p class="lead">Hi, I'm [Your Name]!</p>
        
        <h3>Background</h3>
        <p>I'm a software developer with a passion for creating innovative solutions. 
           I specialize in C# and .NET development, and I love learning new technologies.</p>
        
        <h3>Skills</h3>
        <ul>
            <li>C# and .NET Core</li>
            <li>ASP.NET MVC</li>
            <li>HTML, CSS, JavaScript</li>
            <li>SQL and Database Design</li>
            <li>Git and Version Control</li>
        </ul>
        
        <h3>Education</h3>
        <p>I'm currently learning C# and .NET development through hands-on projects.</p>
    </div>
    <div class="col-md-4">
        <div class="card">
            <div class="card-body">
                <h5 class="card-title">Quick Facts</h5>
                <ul class="list-unstyled">
                    <li><strong>Location:</strong> [Your City]</li>
                    <li><strong>Experience:</strong> [X] years</li>
                    <li><strong>Interests:</strong> Programming, Reading, Travel</li>
                </ul>
            </div>
        </div>
    </div>
</div>
```

### Contact Page (Views/Contact/Index.cshtml)
```html
@{
    ViewData["Title"] = "Contact";
}

<div class="row">
    <div class="col-md-6">
        <h1>Contact Me</h1>
        <p class="lead">Get in touch with me!</p>
        
        <form>
            <div class="form-group">
                <label for="name">Name</label>
                <input type="text" class="form-control" id="name" name="name" required>
            </div>
            
            <div class="form-group">
                <label for="email">Email</label>
                <input type="email" class="form-control" id="email" name="email" required>
            </div>
            
            <div class="form-group">
                <label for="subject">Subject</label>
                <input type="text" class="form-control" id="subject" name="subject" required>
            </div>
            
            <div class="form-group">
                <label for="message">Message</label>
                <textarea class="form-control" id="message" name="message" rows="5" required></textarea>
            </div>
            
            <button type="submit" class="btn btn-primary">Send Message</button>
        </form>
    </div>
    
    <div class="col-md-6">
        <h3>Other Ways to Reach Me</h3>
        <ul class="list-unstyled">
            <li><strong>Email:</strong> your.email@example.com</li>
            <li><strong>LinkedIn:</strong> linkedin.com/in/yourprofile</li>
            <li><strong>GitHub:</strong> github.com/yourusername</li>
        </ul>
    </div>
</div>
```

### Portfolio Page (Views/Portfolio/Index.cshtml)
```html
@{
    ViewData["Title"] = "Portfolio";
}

<h1>My Portfolio</h1>
<p class="lead">Here are some of my recent projects:</p>

<div class="row">
    <div class="col-md-4 mb-4">
        <div class="card">
            <div class="card-body">
                <h5 class="card-title">Calculator App</h5>
                <p class="card-text">A simple console calculator built with C#.</p>
                <p class="text-muted">Technologies: C#, .NET Core</p>
                <a href="#" class="btn btn-primary">View Project</a>
            </div>
        </div>
    </div>
    
    <div class="col-md-4 mb-4">
        <div class="card">
            <div class="card-body">
                <h5 class="card-title">To-Do List Manager</h5>
                <p class="card-text">A console application for managing tasks.</p>
                <p class="text-muted">Technologies: C#, Collections, File I/O</p>
                <a href="#" class="btn btn-primary">View Project</a>
            </div>
        </div>
    </div>
    
    <div class="col-md-4 mb-4">
        <div class="card">
            <div class="card-body">
                <h5 class="card-title">Weather Data Analyzer</h5>
                <p class="card-text">Analyzes weather data from CSV files.</p>
                <p class="text-muted">Technologies: C#, LINQ, File I/O</p>
                <a href="#" class="btn btn-primary">View Project</a>
            </div>
        </div>
    </div>
</div>
```

### Custom CSS (wwwroot/css/site.css)
```css
/* Add your custom styles here */
body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.navbar-brand {
    font-weight: bold;
    font-size: 1.5rem;
}

.card {
    transition: transform 0.2s;
}

.card:hover {
    transform: translateY(-5px);
    box-shadow: 0 4px 8px rgba(0,0,0,0.1);
}

.btn-primary {
    background-color: #007bff;
    border-color: #007bff;
}

.btn-primary:hover {
    background-color: #0056b3;
    border-color: #0056b3;
}

.footer {
    position: relative;
    bottom: 0;
    width: 100%;
    white-space: nowrap;
    line-height: 60px;
}
```

## Common Mistakes to Avoid

### ❌ Don't Do This:
```html
<!-- Not using proper MVC structure -->
<div>Content here</div> <!-- Should be in Views folder -->

<!-- Not using layout -->
<html><head>...</head><body>...</body></html> <!-- Should use _Layout.cshtml -->

<!-- Not using Bootstrap classes -->
<div>Content</div> <!-- Should use Bootstrap grid system -->

<!-- Not using proper routing -->
<a href="/about">About</a> <!-- Should use asp-controller and asp-action -->
```

### ✅ Do This Instead:
```html
<!-- Use proper MVC structure -->
@{
    ViewData["Title"] = "Page Title";
}
<div>Content here</div>

<!-- Use layout -->
@{
    Layout = "_Layout";
}

<!-- Use Bootstrap classes -->
<div class="container">
    <div class="row">
        <div class="col-md-6">Content</div>
    </div>
</div>

<!-- Use proper routing -->
<a asp-controller="About" asp-action="Index">About</a>
```

## Testing Your Website

### Test Cases to Try:
1. **Navigation**: Click all navigation links → Should work correctly
2. **Responsive design**: Resize browser window → Should adapt
3. **Contact form**: Fill out and submit form → Should work (basic validation)
4. **Cross-browser**: Test in different browsers → Should work consistently
5. **Mobile view**: Test on mobile device or emulator → Should be responsive

### How to Run:
```bash
dotnet run
```
Then open `https://localhost:5001` in your browser.

## Submission Checklist
- [ ] Project compiles without errors
- [ ] All pages load correctly
- [ ] Navigation works between all pages
- [ ] Contact form has proper validation
- [ ] Website is responsive (works on mobile)
- [ ] CSS styling is applied correctly
- [ ] Content is personalized and professional
- [ ] Code follows MVC pattern properly
- [ ] Website is visually appealing

## Bonus Challenges
1. **Contact Form Functionality**: Make the contact form actually send emails
2. **Blog Section**: Add a blog with articles
3. **Image Gallery**: Add a photo gallery to portfolio
4. **Dark Theme**: Add a theme toggle
5. **Animations**: Add CSS animations and transitions
6. **SEO Optimization**: Add meta tags and descriptions
7. **Social Media Integration**: Add social media links
8. **Deployment**: Deploy to Azure or other hosting service

## Resources
- [ASP.NET Core MVC](https://docs.microsoft.com/en-us/aspnet/core/mvc/)
- [Bootstrap Documentation](https://getbootstrap.com/docs/)
- [HTML/CSS Basics](https://developer.mozilla.org/en-US/docs/Web/HTML)
- [Razor Syntax](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/razor)

## Next Steps
Once you complete this project, you'll be ready for Project 8: Guest Book, where you'll learn about form handling, model binding, and data persistence. 