## Contact Me

I’d love to hear from you! Whether you have a question about my projects, want to collaborate, or just want to say hello, feel free to reach out using the form below or via any of the direct channels listed.

### Reach Out Directly

- Email: `theophiluse <at> xiitechnologies <dot> com`.
- Discord: `theophiluse`.

{{< rawhtml >}}

<style>
  /* Container & Layout */
  .form-wrapper {
    max-width: 600px;
    margin: 2rem auto;
    padding: 2rem;
    background: #ffffff;
    border-radius: 12px;
    box-shadow: 0 8px 24px rgba(0,0,0,0.08);
    font-family: 'Segoe UI', sans-serif;
  }

  /* Headings */
  .form-wrapper h2 {
    font-size: 1.8rem;
    margin-bottom: 1rem;
    color: #333;
    text-align: center;
  }

  /* Form Fields */
  .form-group {
    display: flex;
    flex-direction: column;
    margin-bottom: 1.2rem;
  }
  .form-group label {
    margin-bottom: 0.4rem;
    font-weight: 600;
    color: #555;
  }
  .form-group input,
  .form-group textarea {
    padding: 0.8rem 1rem;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 1rem;
    transition: border-color 0.2s ease, box-shadow 0.2s ease;
  }
  .form-group input:focus,
  .form-group textarea:focus {
    border-color: #2a9d8f;
    box-shadow: 0 0 0 3px rgba(42, 157, 143, 0.15);
    outline: none;
  }
  .form-group textarea {
    resize: vertical;
    min-height: 140px;
  }

  /* Submit Button */
  .form-wrapper button[type="submit"] {
    width: 100%;
    padding: 0.9rem;
    background: linear-gradient(135deg, #2a9d8f 0%, #218c7d 100%);
    border: none;
    border-radius: 8px;
    color: #fff;
    font-size: 1.1rem;
    font-weight: 600;
    cursor: pointer;
    transition: background 0.3s ease, transform 0.2s ease;
  }
  .form-wrapper button[type="submit"]:hover {
    background: linear-gradient(135deg, #218c7d 0%, #1a746a 100%);
    transform: translateY(-2px);
  }
  .form-wrapper button[type="submit"]:active {
    transform: translateY(0);
  }

  /* Responsive */
  @media (max-width: 480px) {
    .form-wrapper {
      padding: 1.5rem;
    }
    .form-wrapper h2 {
      font-size: 1.6rem;
    }
  }
</style>

<div class="form-wrapper">
  <h2>Send a Message</h2>
  <form action="https://formsubmit.co/17b3dc57a7a0c7e50a994fc8746be041" method="POST">
    <!-- Disable Captcha -->
    <input type="hidden" name="_captcha" value="false">
    <!-- Redirect after submit (optional) -->
    <input type="hidden" name="_next" value="https://theophiluse.xiitechnologies.com/contact">

    <div class="form-group">
      <label for="name">Your Name</label>
      <input id="name" name="Name" type="text" placeholder="Jane Doe" required>
    </div>

    <div class="form-group">
      <label for="email">Your Email</label>
      <input id="email" name="Email" type="email" placeholder="name@example.com" required>
    </div>

    <div class="form-group">
      <label for="subject">Subject</label>
      <input id="subject" name="Subject" type="text" placeholder="Project inquiry, feedback, etc.">
    </div>

    <div class="form-group">
      <label for="message">Message</label>
      <textarea id="message" name="Message" placeholder="Leave a comment..." required></textarea>
    </div>

    <button type="submit">Send Message</button>

  </form>
</div>

<script>
  document.querySelector('form').addEventListener('submit', e => {
    const email = e.target.email.value.trim();
    if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email)) {
      alert('Please enter a valid email address.');
      e.preventDefault();
    }
  });
</script>

{{< /rawhtml >}}
