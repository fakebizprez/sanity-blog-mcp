# LLM Prompt: Create Agent Actions Blog Template with NextJS/Sanity

## Context
You are tasked with creating a complete, production-ready blog template based on the "Agent Actions: AI building blocks for structured content" blog post design. This should be a reusable template system built with NextJS 14+, TypeScript, React, and Sanity headless CMS.

## Design Analysis
The blog page features:
- **Hero Section**: Large title with subtitle description
- **Video/Media Section**: YouTube video embed with decorative graphics
- **Content Structure**: Multiple sections with headings, paragraphs, code blocks, and lists
- **Navigation**: Table of contents/jump links on the right side
- **Interactive Elements**: Copy buttons on code blocks, syntax highlighting
- **Responsive Design**: Clean, modern layout with proper spacing
- **Typography**: Clear hierarchy with various text sizes and weights

## Technical Requirements

### Core Stack
- **Framework**: NextJS 14+ (App Router)
- **Language**: TypeScript
- **Styling**: Tailwind CSS
- **CMS**: Sanity with `next-sanity` integration
- **Code Highlighting**: Prism.js or similar
- **Video**: YouTube embed support

### Sanity Schema Requirements
Create comprehensive schemas for:

1. **Blog Post Schema** (`blogPost.ts`):
   ```typescript
   // Required fields:
   - title: string (required)
   - slug: slug (required, generated from title)
   - publishedAt: datetime
   - author: reference to author
   - excerpt: text (meta description)
   - heroImage: image (optional)
   - videoUrl: url (YouTube/Vimeo)
   - categories: array of references
   - tags: array of strings
   - content: array of portable text blocks
   - tableOfContents: boolean (auto-generate TOC)
   - readingTime: number (calculated)
   - featured: boolean
   - status: string (draft/published)
   ```

2. **Author Schema** (`author.ts`):
   ```typescript
   - name: string
   - slug: slug
   - bio: text
   - avatar: image
   - social: object (twitter, linkedin, github)
   - role: string
   ```

3. **Category Schema** (`category.ts`):
   ```typescript
   - name: string
   - slug: slug
   - description: text
   - color: color
   ```

4. **Portable Text Configuration**:
   - Support for headings (h1-h6)
   - Code blocks with language specification
   - Lists (ordered/unordered)
   - Custom blocks for:
     - YouTube video embeds
     - Image galleries
     - Call-to-action sections
     - Code snippets with copy functionality
     - Pro tips/notes/warnings

### NextJS Implementation Requirements

1. **File Structure**:
   ```
   src/
   ├── app/
   │   ├── blog/
   │   │   ├── [slug]/
   │   │   │   └── page.tsx
   │   │   └── page.tsx
   │   ├── studio/
   │   │   └── [[...tool]]/
   │   │       └── page.tsx
   │   └── globals.css
   ├── components/
   │   ├── blog/
   │   │   ├── BlogPost.tsx
   │   │   ├── BlogCard.tsx
   │   │   ├── TableOfContents.tsx
   │   │   ├── CodeBlock.tsx
   │   │   └── VideoEmbed.tsx
   │   └── ui/
   ├── sanity/
   │   ├── schemas/
   │   ├── lib/
   │   └── types.ts
   └── lib/
   ```

2. **Key Components**:

   **BlogPost Component**:
   - Dynamic table of contents generation
   - Reading progress indicator
   - Social sharing buttons
   - Related posts section
   - Author bio section
   - Copy-to-clipboard for code blocks

   **TableOfContents Component**:
   - Auto-generate from headings in content
   - Smooth scroll to sections
   - Highlight active section
   - Sticky positioning on desktop

   **CodeBlock Component**:
   - Syntax highlighting
   - Language detection
   - Copy button with success feedback
   - Line numbers (optional)
   - Theme support (dark/light)

   **VideoEmbed Component**:
   - YouTube/Vimeo support
   - Responsive sizing
   - Lazy loading
   - Thumbnail previews

3. **Data Fetching**:
   - Use `sanityFetch` for server-side data fetching
   - Implement ISR (Incremental Static Regeneration)
   - Add proper revalidation tags
   - Support draft mode for content preview

4. **SEO & Performance**:
   - Generate OpenGraph images
   - Meta tags from Sanity content
   - JSON-LD structured data
   - Optimized image loading
   - Lazy loading for non-critical content

### Styling Requirements
- **Design System**: Create consistent spacing, typography, and color scales
- **Responsive**: Mobile-first approach with breakpoints
- **Dark Mode**: Support system preference and manual toggle
- **Animations**: Subtle transitions and hover effects
- **Accessibility**: WCAG 2.1 AA compliance

### Content Management Features
1. **Rich Text Editor**: Custom portable text components
2. **Media Management**: Image optimization and CDN integration
3. **Preview Mode**: Real-time content preview
4. **Versioning**: Draft/published states
5. **SEO Tools**: Meta descriptions, slugs, social images

## Implementation Instructions

### Phase 1: Project Setup
1. Initialize NextJS project with TypeScript
2. Install and configure Sanity
3. Set up Tailwind CSS
4. Configure environment variables
5. Create basic file structure

### Phase 2: Sanity Configuration
1. Define schemas with proper validation
2. Configure Sanity Studio
3. Set up portable text components
4. Create sample content
5. Configure image pipeline

### Phase 3: Frontend Development
1. Create layout components
2. Implement blog post page
3. Build blog listing page
4. Add interactive features
5. Implement search functionality

### Phase 4: Advanced Features
1. Add table of contents generation
2. Implement code syntax highlighting
3. Create video embedding system
4. Add social sharing
5. Build related posts logic

### Phase 5: Optimization
1. SEO optimization
2. Performance tuning
3. Accessibility improvements
4. Error handling
5. Testing setup

## Expected Deliverables
1. Complete NextJS application with TypeScript
2. Sanity schemas and studio configuration
3. Responsive blog template matching the design
4. Interactive features (TOC, code copying, etc.)
5. Documentation for setup and customization
6. Sample content and data

## Success Criteria
- Pixel-perfect recreation of the design aesthetic
- Fully functional CMS integration
- Performance score >90 on Lighthouse
- Mobile-responsive design
- Accessible to WCAG 2.1 AA standards
- Easily customizable for different brands/styles
- Production-ready code quality

## Additional Considerations
- Consider implementing a search feature using Sanity's search capabilities
- Add RSS feed generation
- Include newsletter signup integration
- Support for multiple authors
- Comment system integration (optional)
- Analytics integration
- Error boundaries and loading states

Focus on creating a maintainable, scalable solution that can serve as a foundation for various blog implementations while staying true to the visual design and user experience of the original Agent Actions blog post.
