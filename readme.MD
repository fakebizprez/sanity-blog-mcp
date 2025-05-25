```markdown
# Blog Page Implementation Guide for Next.js with Sanity CMS

## Overview

This guide outlines how to create a dynamic blog page that displays blog posts from Sanity CMS using Next.js. The implementation features a featured post layout where the most recent post appears as a large hero card, followed by older posts in a grid layout.

## Key Implementation Details

### 1. Sanity Schema for Blog Posts

First, ensure your Sanity schema includes these fields. Create or modify the `sanity/schemaTypes/post.ts` file with the following content:

```
// sanity/schemaTypes/post.ts

export default {
  name: 'post',
  title: 'Blog Post',
  type: 'document',
  fields: [
    {
      name: 'title',
      title: 'Title',
      type: 'string',
      validation: (Rule) => Rule.required(),
    },
    {
      name: 'slug',
      title: 'Slug',
      type: 'slug',
      options: {
        source: 'title',
        maxLength: 96,
      },
      validation: (Rule) => Rule.required(),
    },
    {
      name: 'excerpt',
      title: 'Excerpt',
      type: 'text',
      rows: 3,
      description: 'Brief description of the post',
    },
    {
      name: 'mainImage',
      title: 'Main Image',
      type: 'image',
      options: {
        hotspot: true,
      },
    },
    {
      name: 'author',
      title: 'Author',
      type: 'reference',
      to: [{ type: 'author' }],
    },
    {
      name: 'categories',
      title: 'Categories',
      type: 'array',
      of: [{ type: 'reference', to: { type: 'category' } }],
    },
    {
      name: 'publishedAt',
      title: 'Published at',
      type: 'datetime',
      validation: (Rule) => Rule.required(),
    },
    {
      name: 'body',
      title: 'Body',
      type: 'blockContent', // Rich text field
    },
  ],
  preview: {
    select: {
      title: 'title',
      author: 'author.name',
      media: 'mainImage',
    },
  },
};
```

### 2. Blog Page Component Structure

Create or modify the `app/blog/page.tsx` file with the following content:

```
// app/blog/page.tsx

import { Suspense } from 'react';
import { groq } from 'next-sanity';
import { sanityClient } from '@/lib/sanity.client';
import BlogGrid from '@/components/blog-grid';
import FeaturedPost from '@/components/featured-post';
import { Post } from '@/types/blog';

// GROQ query to fetch blog posts
const postsQuery = groq`
  *[_type == "post"] | order(publishedAt desc) {
    _id,
    title,
    slug,
    excerpt,
    publishedAt,
    mainImage,
    "author": author->{
      name,
      image
    },
    "categories": categories[]->title
  }
`;

async function getPosts(): Promise<Post[]> {
  return sanityClient.fetch(postsQuery);
}

export default async function BlogPage() {
  const posts = await getPosts();
  const [featuredPost, ...olderPosts] = posts;

  return (
    <div className="min-h-screen bg-black text-white">
      <div className="container mx-auto px-4 py-12">
        <h1 className="text-5xl font-bold mb-12">Blog</h1>

        {/* Featured Post (Most Recent) */}
        {featuredPost && (
          <Suspense fallback={<div>Loading featured post...</div>}>
            <FeaturedPost post={featuredPost} />
          </Suspense>
        )}

        {/* Older Posts Grid */}
        {olderPosts.length > 0 && (
          <Suspense fallback={<div>Loading posts...</div>}>
            <BlogGrid posts={olderPosts} />
          </Suspense>
        )}
      </div>
    </div>
  );
}
```

### 3. Featured Post Component

Create or modify the `components/featured-post.tsx` file with the following content:

```
// components/featured-post.tsx

import Link from 'next/link';
import Image from 'next/image';
import { urlForImage } from '@/lib/sanity.image';
import { Post } from '@/types/blog';
import { format } from 'date-fns';

interface FeaturedPostProps {
  post: Post;
}

export default function FeaturedPost({ post }: FeaturedPostProps) {
  return (
    <Link href={`/blog/${post.slug.current}`}>
      <article className="relative mb-16 group cursor-pointer">
        <div className="relative h-[500px] w-full overflow-hidden rounded-lg bg-gray-900">
          {post.mainImage && (
            <Image
              src={urlForImage(post.mainImage).url()}
              alt={post.title}
              fill
              className="object-cover transition-transform duration-300 group-hover:scale-105"
            />
          )}
          <div className="absolute inset-0 bg-gradient-to-t from-black/80 to-transparent" />

          <div className="absolute bottom-0 left-0 right-0 p-8">
            <div className="flex items-center gap-2 text-sm text-gray-300 mb-3">
              {post.categories?. && (
                <span className="uppercase tracking-wider">{post.categories}</span>
              )}
              <span className="text-gray-400">
                {format(new Date(post.publishedAt), 'MMM d')}
              </span>
            </div>

            <h2 className="text-4xl font-bold mb-3 group-hover:text-gray-200 transition-colors">
              {post.title}
            </h2>

            <p className="text-gray-300 text-lg mb-4 line-clamp-2">
              {post.excerpt}
            </p>

            {post.author && (
              <div className="flex items-center gap-3">
                {post.author.image && (
                  <Image
                    src={urlForImage(post.author.image).url()}
                    alt={post.author.name}
                    width={40}
                    height={40}
                    className="rounded-full"
                  />
                )}
                <span className="text-sm text-gray-300">{post.author.name}</span>
              </div>
            )}
          </div>
        </div>
      </article>
    </Link>
  );
}
```

### 4. Blog Grid Component

Create or modify the `components/blog-grid.tsx` file with the following content:

```
// components/blog-grid.tsx

import Link from 'next/link';
import Image from 'next/image';
import { urlForImage } from '@/lib/sanity.image';
import { Post } from '@/types/blog';
import { format } from 'date-fns';

interface BlogGridProps {
  posts: Post[];
}

export default function BlogGrid({ posts }: BlogGridProps) {
  return (
    <div className="grid md:grid-cols-2 lg:grid-cols-3 gap-6">
      {posts.map((post) => (
        <Link key={post._id} href={`/blog/${post.slug.current}`}>
          <article className="group cursor-pointer">
            <div className="relative h-64 w-full overflow-hidden rounded-lg bg-gray-900 mb-4">
              {post.mainImage && (
                <Image
                  src={urlForImage(post.mainImage).url()}
                  alt={post.title}
                  fill
                  className="object-cover transition-transform duration-300 group-hover:scale-105"
                />
              )}
            </div>

            <div className="space-y-2">
              <div className="flex items-center gap-2 text-xs text-gray-400">
                {post.categories?. && (
                  <span className="uppercase tracking-wider">{post.categories}</span>
                )}
                <span>{format(new Date(post.publishedAt), 'MMM d')}</span>
              </div>

              <h3 className="text-xl font-semibold group-hover:text-gray-300 transition-colors line-clamp-2">
