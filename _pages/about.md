---
layout: default
title: About
permalink: /about/
---

<div class="about-page">
    <div class="container">
        <header class="page-header">
            <h1>About Me</h1>
            <p class="lead">Get to know me better</p>
        </header>

        <div class="about-content">
            <div class="about-section">
                <h2>Who I Am</h2>
                <p>Hello! I'm Savitha Raghunathan, a Senior Software Engineer at Red Hat working on Container Migration and Application Modernization technologies. I'm also a CNCF Ambassador and Konveyor project maintainer.</p>
                
                <p>My passion lies in nurturing the Open Source community, where I find great joy in mentoring new contributors and empowering them to make significant contributions. I'm currently exploring AI-assisted code modernization through prompt engineering, working on the Konveyor AI initiative to help large language models generate actionable, context-aware refactoring hints.</p>
            </div>

            <div class="about-section">
                <h2>My Background</h2>
                <p>I have a diverse background spanning over 12 years in software engineering, with deep expertise in container and cloud technologies. Prior to Red Hat, I spent 5+ years at MathWorks designing and developing greenfield and brownfield PaaS Infrastructure, where I reduced Kubernetes cluster provisioning time by 50% and hosting costs by 10x.</p>
                
                <p>I've led the Kubernetes 1.22 Release Team, currently serve as K8s SIG-Security Documentation Lead, and contributed significantly to OpenShift APIs for Data Protection (OADP). My journey has been focused on building scalable infrastructure, fostering open source communities, and driving innovation in cloud native technologies.</p>
            </div>

            <div class="about-section">
                <h2>What I Do</h2>
                <ul>
                    <li><strong>AI-Assisted Code Modernization:</strong> Working on Konveyor AI initiative with prompt engineering for large language models</li>
                    <li><strong>Open Source Leadership:</strong> Leading Konveyor community as Tech Lead and CNCF Ambassador</li>
                    <li><strong>Kubernetes Community:</strong> K8s SIG-Security Documentation Lead and former Release Team Lead</li>
                    <li><strong>Technical Mentoring:</strong> Supporting new contributors and fostering inclusive open source communities</li>
                    <li><strong>Conference Speaking:</strong> Representing projects at major events like KubeCon and conducting workshops</li>
                </ul>
            </div>

            <div class="about-section">
                <h2>Get in Touch</h2>
                <p>I'm always interested in connecting with fellow technologists, open source contributors, and anyone passionate about cloud native technologies. Feel free to reach out if you'd like to collaborate, discuss technology, or just say hello!</p>
                
                <div class="contact-links">
                    <a href="https://github.com/savitharaghunathan" class="contact-link" target="_blank">
                        <span class="contact-icon">🐙</span>
                        GitHub
                    </a>
                    <a href="https://linkedin.com/in/savitharaghunathan" class="contact-link" target="_blank">
                        <span class="contact-icon">💼</span>
                        LinkedIn
                    </a>
        
                </div>
            </div>
        </div>
    </div>
</div>

<style>
.about-page {
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

.about-content {
    max-width: 800px;
    margin: 0 auto;
}

.about-section {
    margin-bottom: 3rem;
}

.about-section h2 {
    color: #1f2937;
    font-size: 1.75rem;
    margin-bottom: 1rem;
    padding-bottom: 0.5rem;
    border-bottom: 2px solid #e5e7eb;
}

.about-section p {
    color: #374151;
    line-height: 1.8;
    margin-bottom: 1rem;
}

.about-section ul {
    list-style: none;
    padding: 0;
}

.about-section li {
    padding: 0.5rem 0;
    border-bottom: 1px solid #f3f4f6;
    color: #374151;
}

.about-section li:last-child {
    border-bottom: none;
}

.contact-links {
    display: flex;
    gap: 1rem;
    flex-wrap: wrap;
    margin-top: 1rem;
}

.contact-link {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    padding: 0.75rem 1.5rem;
    background: #f3f4f6;
    color: #374151;
    text-decoration: none;
    border-radius: 8px;
    transition: all 0.3s ease;
}

.contact-link:hover {
    background: #3b82f6;
    color: white;
    transform: translateY(-2px);
}

.contact-icon {
    font-size: 1.2rem;
}

@media (max-width: 768px) {
    .page-header h1 {
        font-size: 2rem;
    }
    
    .contact-links {
        flex-direction: column;
    }
    
    .contact-link {
        justify-content: center;
    }
}
</style> 