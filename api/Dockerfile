# Use the official PHP image with Apache
FROM php:8.2-apache

# Install necessary PHP extensions and git
RUN apt-get update && apt-get install -y \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    git \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install gd pdo pdo_mysql

# Enable Apache mod_rewrite
RUN a2enmod rewrite

# Set the working directory
WORKDIR /var/www/html

# Copy the current directory contents into the container at /var/www/html
COPY . .

# Set DocumentRoot to the public folder
RUN sed -i 's|/var/www/html|/var/www/html/public|g' /etc/apache2/sites-available/000-default.conf

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install Laravel dependencies
RUN composer install --optimize-autoloader --no-dev

#RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf

# Set the permissions for the storage and bootstrap/cache directories
RUN chown -R www-data:www-data storage bootstrap/cache

# Expose the port Apache is running on
EXPOSE 80

# Start the Apache server
CMD ["apache2-foreground"]