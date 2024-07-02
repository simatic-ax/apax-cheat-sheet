# creating a catalog
apax create catalog <catalog-name>
apax add @scope/packagename@version
apax pack
apax publish

# using a catalog
apax add --catalog <catalog-name>
apax update     // no catalog lookup  
apax update --catalog  // only looks for catalogs updates
apax install    // all including catalogs
apax install --catalog (--strict)  // all only from catalogs
apax remove git push -u origin master