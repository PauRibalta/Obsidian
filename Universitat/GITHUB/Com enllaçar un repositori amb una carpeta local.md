

#### 1. Enllaçar el repositori amb la carpeta local 
Entra a la carpeta de la vault: cd cami/a/la/teva/vault  
Inicialitza Git: git init  
Enllaça amb GitHub: git remote add origin [https://github.com/usuari/nom-repo.git](https://github.com/usuari/nom-repo.git "https://github.com/usuari/nom-repo.git")  
Comprova: git remote -v 
#### 2. Pujar el contingut 
git add . 
git commit -m "Primer commit: vault d'Obsidian" 
git branch -M main 
git push -u origin main 
#### ![🔄](https://discord.com/assets/e541f62450f233be.svg) Flux normal de treball 
Després, cada cop que facis canvis: 
git add . 
git commit -m "Actualitza notes" 
git push

### Desfer canvis en fitxers

`git restore .`serveix per **desfer canvis en fitxers** i tornar-los a l’estat que tenien en algun punt del repositori (normalment l’últim commit).