Optimisation du site :

Ce qu’il faut changer
Remplace le bloc par celui-ci :

var imgs=[];

for(var i=1;i<=NB_PHOTOS;i++){
  var img=document.createElement('img');

  img.src=DOSSIER+'/'+PREFIXE+'-'+(i<10?'0':'')+i+'.webp';
  img.alt=PREFIXE+' '+i;
  img.draggable=false;

  /* base perf */
  img.loading="lazy";
  img.decoding="async";
  img.fetchPriority="low";

  gal.appendChild(img);
  imgs.push(img);
}

ajoute CE CODE après :

let lastScroll = 0;

function smartEngine(){
  const direction = gs.scrollLeft > lastScroll ? 1 : -1;
  lastScroll = gs.scrollLeft;

  const index = Math.floor(gs.scrollLeft / window.innerWidth);

  for(let i=0;i<imgs.length;i++){
    const img = imgs[i];

    // images proches = priorité haute
    if(Math.abs(i - index) <= 2){
      img.loading = "eager";
      img.fetchPriority = "high";
    }

    // images dans la direction du scroll = préload anticipé
    if(direction === 1 && i === index + 3){
      img.loading = "eager";
      img.fetchPriority = "high";
    }

    if(direction === -1 && i === index - 3){
      img.loading = "eager";
      img.fetchPriority = "high";
    }
  }
}

ajoute aussi :

const observer=new IntersectionObserver((entries)=>{
  for(const e of entries){
    if(e.isIntersecting){
      const img=e.target;

      img.loading="eager";
      img.fetchPriority="high";

      observer.unobserve(img);
    }
  }
},{
  root: gs,
  rootMargin: "350px",
  threshold: 0.1
});

imgs.forEach(img=>observer.observe(img));


gs.addEventListener('scroll', smartEngine, {passive:true});

et
.galerie-scroll {
  scroll-behavior: smooth;
}

.galerie img {
  will-change: transform;
  transform: translateZ(0);
}



