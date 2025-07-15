---
layout: default
title: Blog
permalink: /blog/
---

<div class="blog-list">
    <div class="container">
        <header class="page-header">
            <h1>Blog</h1>
            <p class="lead">Thoughts, ideas, and experiences shared through writing</p>
        </header>

        {% assign posts_by_year = site.posts | group_by_exp: "post", "post.date | date: '%Y'" | sort: "name" | reverse %}
        
        {% for year in posts_by_year %}
        <div class="year-group">
            <h2>{{ year.name }}</h2>
            <div class="blog-grid">
                {% for post in year.items %}
                <article class="blog-item">
                    <div class="blog-item-content">
                        <h2><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
                        <div class="blog-item-meta">
                            <time datetime="{{ post.date | date_to_xmlschema }}">
                                {{ post.date | date: "%B %d, %Y" }}
                            </time>
                            {% if post.categories %}
                            <span class="blog-categories">
                                {% for category in post.categories %}
                                <span class="category-tag">{{ category }}</span>
                                {% endfor %}
                            </span>
                            {% endif %}
                        </div>
                        {% if post.excerpt %}
                        <div class="blog-item-excerpt">
                            {{ post.excerpt | strip_html | truncatewords: 30 }}
                        </div>
                        {% endif %}
                        <a href="{{ post.url | relative_url }}" class="read-more">Read more →</a>
                    </div>
                </article>
                {% endfor %}
            </div>
        </div>
        {% endfor %}

        {% if paginator.total_pages > 1 %}
        <div class="pagination">
            {% if paginator.previous_page %}
            <a href="{{ paginator.previous_page_path | relative_url }}" class="prev-page">
                ← Previous
            </a>
            {% endif %}

            <span class="page-info">
                Page {{ paginator.page }} of {{ paginator.total_pages }}
            </span>

            {% if paginator.next_page %}
            <a href="{{ paginator.next_page_path | relative_url }}" class="next-page">
                Next →
            </a>
            {% endif %}
        </div>
        {% endif %}
    </div>
</div>

<style>
.blog-list {
    padding: 3rem 0;
}

.page-header {
    text-align: center;
    margin-bottom: 3rem;
}

.page-header h1 {
    font-size: 2.5rem;
    color: #1f2937;
    margin-bottom: 0.5rem;
}

.lead {
    font-size: 1.25rem;
    color: #6b7280;
}

.year-group {
    margin-bottom: 4rem;
}

.year-group h2 {
    color: #1f2937;
    font-size: 2rem;
    margin-bottom: 2rem;
    padding-bottom: 0.5rem;
    border-bottom: 2px solid #e5e7eb;
}

.blog-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(350px, 1fr));
    gap: 2rem;
}

.blog-item {
    background: #fff;
    border-radius: 12px;
    box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
    overflow: hidden;
    transition: transform 0.3s ease, box-shadow 0.3s ease;
    border: 1px solid #e5e7eb;
}

.blog-item:hover {
    transform: translateY(-4px);
    box-shadow: 0 10px 25px -3px rgba(0, 0, 0, 0.1);
}

.blog-item-content {
    padding: 1.5rem;
}

.blog-item h2 {
    margin-bottom: 0.75rem;
    font-size: 1.25rem;
    line-height: 1.4;
}

.blog-item h2 a {
    color: #1f2937;
    text-decoration: none;
    transition: color 0.3s ease;
}

.blog-item h2 a:hover {
    color: #3b82f6;
}

.blog-item-meta {
    color: #6b7280;
    font-size: 0.9rem;
    margin-bottom: 1rem;
    display: flex;
    flex-wrap: wrap;
    gap: 0.5rem;
    align-items: center;
}

.blog-categories {
    display: flex;
    gap: 0.25rem;
}

.blog-item-excerpt {
    color: #6b7280;
    line-height: 1.6;
    margin-bottom: 1rem;
}

.read-more {
    color: #3b82f6;
    text-decoration: none;
    font-weight: 500;
    transition: color 0.3s ease;
}

.read-more:hover {
    color: #2563eb;
}

.pagination {
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 1rem;
    margin: 3rem 0;
    flex-wrap: wrap;
}

.pagination a,
.pagination span {
    padding: 0.75rem 1.5rem;
    border: 1px solid #e5e7eb;
    border-radius: 8px;
    text-decoration: none;
    color: #6b7280;
    transition: all 0.3s ease;
    background: #fff;
}

.pagination a:hover {
    background: #3b82f6;
    color: white;
    border-color: #3b82f6;
}

.page-info {
    color: #6b7280;
    font-weight: 500;
}

@media (max-width: 768px) {
    .page-header h1 {
        font-size: 2rem;
    }
    
    .blog-grid {
        grid-template-columns: 1fr;
    }
    
    .blog-item-meta {
        flex-direction: column;
        align-items: flex-start;
    }
    
    .pagination {
        flex-direction: column;
        gap: 0.5rem;
    }
}
</style> 