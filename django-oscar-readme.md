How to fork app
command: oscar_fork_app
Example:
If we want to fork oscar/apps/catalogue in our project_name/apps/
python manage.py oscar_fork_app catalogue project_name/apps/

If we want to fork subapps like oscar/apps/dashboard/catalogue/ then we need to first fork dashbaord app
python manage.py oscar_fork_app dashboard project_name/apps/
then we fork catalogue app
python manage.py oscar_fork_app catalogue_dashboard project_name/apps/ # catalogue_dashboard name came form dashboard/catalogue/apps.py where label_name is catalogue_dashboard
