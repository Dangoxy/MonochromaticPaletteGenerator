<template>
    <div class="flex flex-col h-full w-full p-4 justify-start items-center gap-4 bg-[#1E1E1E]">
        <h1 class="text-white text-[32px] text-center">Monochromatic color palette generator</h1>

        <div class="flex flex-col gap-2 w-[100%] max-w-96">
            <div class="flex justify-center items-center w-full gap-2 text-[#fff8ff]">
                <label class="opacity-0">G</label>
                <label class="-m-2">R</label>
                <label class="opacity-0">B</label>
                <input class="rounded-md border-2 px-2 w-full text-[#1E1E1E]" v-model="rgb.r" placeholder="0" maxlength="3">
                <label>G</label>
                <input class="rounded-md border-2 px-2 w-full text-[#1E1E1E]" v-model="rgb.g" placeholder="0" maxlength="3">
                <label>B</label>
                <input class="rounded-md border-2 px-2 w-full text-[#1E1E1E]" v-model="rgb.b" placeholder="0" maxlength="3">
            </div>

            <div class="flex gap-2 justify-center items-center">
                <label class="text-[#fff8ff]">RGB</label>
                <input 
                class="rounded-md border-2 px-2 w-full text-[#1E1E1E]"
                maxlength="16"
                v-model="rgbAll.rgb"
                placeholder="rgb(255,255,255)">
            </div>

            <div class="flex gap-2 justify-center items-center">
                <label class="text-[#fff8ff]">HEX</label>
                <input 
                class="rounded-md border-2 px-2 w-full text-[#1E1E1E]"
                v-model="rgbAll.hex"
                maxlength="8"
                placeholder="#f0f0f0">
            </div>
        </div>

        <div class="flex flex-col gap-0 justify-start items-start w-full overflow-y-auto">
            <div class="flex flex-col  w-full"  
            v-if="palette.length !== 0" 
            v-for="obj,index in palette" 
            
            >
                <div class="flex justify-between items-center gap-2 w-full">
                    <div 
                    class=" flex h-full justify-start items-end p-1 gap-2 w-full phone:min-w-96" 
                    :style="'background-color:' + obj.rgb">
                        
                        <h1 @click="copyContent(obj.rgb)" class="bg-white px-4 py-1  rounded-sm font-bold hoverRGBHandle hoverRgbShow text-[12px] cursor-pointer">RGB</h1>
                        <h1 @click="copyContent(obj.hex)" class="bg-white px-4 py-1 -ml-1 rounded-sm font-bold hoverHEXHandle hoverHexShow text-[12px] cursor-pointer">HEX</h1>
                        
                        <div class="xl:w-[720px] lg:w-64  md:w-[960px] phone:w-32 w-full  "></div>

                        <div class="bg-white rounded-sm rounded-r-none hoverRGB showingClassRgb hidden w-full h-full phone:flex flex-col justify-center phone:items-start items-center px-2">
                            <h1 class=" text-[16px]">{{obj.rgb}}</h1>
                        </div>
                        
                        <div class="bg-white rounded-sm rounded-l-none hoverHEX showingClassHex hidden -ml-2 w-full h-full phone:flex flex-col justify-center phone:items-end items-center px-2">
                            <h1 class=" text-[16px]">{{obj.hex}}</h1>
                        </div>
                        
                    </div>

                    <div 
                    class="lg:flex hidden h-full w-full justify-start items-end p-2 gap-4" 
                    :style="'background-color:' + obj.rgb"></div>

                    <!-- <div 
                    class="w-full p-1 flex justify-center items-center"
                    :style="'background-color:' + obj.rgb">
                        <div class="bg-white w-full h-full flex flex-col justify-center items-start px-2">
                            <h1 class="hoverRGB">{{obj.rgb}}</h1>
                            <h1>{{obj.hex}}</h1>
                        </div>
                    </div> -->
                </div>
                <div v-if="index % 4 == 0" class="h-0.5"></div>
            </div>
        </div>

        <div 
        class="absolute bottom-2 bg-[#1E1E1E] border-2 py-2 px-4 rounded-md"
        :class="showSnackBar.show ? 'flex' : 'hidden'"
        :style="{borderColor: rgbAll.rgb , boxShadow: '0px 0px 4px 0px '+ rgbAll.rgb}">

            <h1 class="text-[#eeeeee]"
            >Copied {{ lastCopiedColor.color }}</h1>

        </div>
    </div>
</template>

<script setup>
import { reactive, watch } from 'vue';

    let rgb = reactive({r:0,g:64,b:64})
    let rgbAll = reactive({rgb:`rgb(${rgb.r},${rgb.g},${rgb.b})`,hex:rgbToHex(rgb.r,rgb.g,rgb.b)})
    let lastCopiedColor = reactive({copied: ""})

    let showSnackBar = reactive({show: false})

    let palette = {rgb:[], hex:[]}

    function rgbToHex(r, g, b){
        return ('#' + [r, g, b].map(x => x.toString(16).padStart(2, '0')).join(''))
    }
    function hexToRgb(hex) {
        var bigint = parseInt(hex, 16);
        var r = (bigint >> 16) & 255;
        var g = (bigint >> 8) & 255;
        var b = bigint & 255;

        return "rgb(" + r + "," + g + "," + b +")";
    }
    function generatePalette(){
        palette = []
        let temp = []
        let tempHex = []
        let tempr = Number(rgb.r)
        let tempg = Number(rgb.g)
        let tempb = Number(rgb.b)

        temp.push({rgb:`rgb(${tempr},${tempg},${tempb})`,hex:rgbToHex(tempr,tempg,tempb)})
        tempHex.push(rgbToHex(tempr,tempg,tempb))
        /* console.log(rgbToHex(tempr,tempg,tempb)) */

        var numArray = 
        [{color: tempr, type:"r"},
        {color: tempg, type:"g"},
        {color: tempb, type:"b"}];

        numArray.sort(function(a, b) {
        return b.color - a.color;
        });

            let dom = numArray[0]
            let subdom = numArray[1]
            let secsubdom = numArray[2]
            /* console.log(numArray) */

        while(dom.color <= 255 || subdom.color <= 255 || secsubdom.color <= 255){

            if((dom.color == subdom.color && dom.color == secsubdom.color) && !(dom.color+16 > 255 || subdom.color+16 > 255 || secsubdom.color+16 > 255)){
                /* console.log(dom.color +" " + subdom.color + " " + secsubdom.color) */
                dom.color+=16
                secsubdom.color+=16
                subdom.color+=16
            }
            else if(dom.color < 255 && !(dom.color+16 > 255 || subdom.color+16 > 255 || secsubdom.color+8 > 255) && (subdom.color !== secsubdom.color)){
                /* console.log(dom.color +" " + subdom.color + " " + secsubdom.color) */
                dom.color+=16
                secsubdom.color+=8
                subdom.color+=16
            }
            else if(!(secsubdom.color+16 > 255 || subdom.color+16 > 255 || dom.color+16 > 255) && (subdom.color == secsubdom.color)){
                /* console.log(dom.color +" " + subdom.color + " " + secsubdom.color) */
                dom.color+=16
                secsubdom.color+=8
                subdom.color+=8
                
            } 
            else if(!(secsubdom.color+16 > 255 || subdom.color+16 > 255 || dom.color+16 > 255) && (subdom.color == secsubdom.color)){
                /* console.log(dom.color +" " + subdom.color + " " + secsubdom.color)  */
                secsubdom.color+=8
                subdom.color+=8
                
            } 
            else if(subdom.color < 255 && !(subdom.color+16 > 255 || secsubdom.color+16 > 255) && subdom.color > secsubdom.color){
                /* console.log(dom.color +" " + subdom.color + " " + secsubdom.color) */
                
                if(subdom.color > secsubdom.color/2){
                    subdom.color+=6
                } 
                else if(subdom.color > secsubdom.color/3){ 
                    subdom.color+=8 
                }
                else if(subdom.color > secsubdom.color/4){ 
                    subdom.color+=12
                }
                else{ 
                    subdom.color+=16
                }

                secsubdom.color+=8
                if( !(dom.color+8 >255) ){
                    dom.color+=8
                }
                
                
            }
            else if(subdom.color < 255 && !(subdom.color+16 > 255 || secsubdom.color+8 > 255) && subdom.color < secsubdom.color){
                /* console.log(dom.color +" " + subdom.color + " " + secsubdom.color) */
                secsubdom.color+=16
                subdom.color+=16
            }
            else if(secsubdom.color < 255 && !(secsubdom.color+16 > 255 || subdom.color+16 > 255)){
                /* console.log(dom.color +" " + subdom.color + " " + secsubdom.color) */
                dom.color += 255-dom.color
                secsubdom.color+=16
                subdom.color+=16
                
            } 
            else if(secsubdom.color < 255 && !(secsubdom.color+16 > 255)){
                /* console.log(dom.color +" " + subdom.color + " " + secsubdom.color) */
                dom.color += 255-dom.color
                subdom.color += 255-subdom.color
                secsubdom.color+=16
                
                
            } 
            else {
                break;
            }
            
            

            let currentr = 
                dom.type === "r" ? 
                dom.color : 
                subdom.type === "r" ? 
                subdom.color : 
                secsubdom.color

            let currentg = 
                dom.type === "g" ? 
                dom.color : 
                subdom.type === "g" ? 
                subdom.color : 
                secsubdom.color

            let currentb = 
                dom.type === "b" ? 
                dom.color : 
                subdom.type === "b" ? 
                subdom.color : 
                secsubdom.color

            temp.push({rgb:`rgb(${currentr},${currentg},${currentb})`,hex:rgbToHex(currentr,currentg,currentb)})
            /* tempHex.push(rgbToHex(currentr,currentg,currentb)) */

            
        }

        /* console.log(temp) */
        palette = temp
        
        rgbAll.rgb = `rgb(${rgb.r !== "" ? rgb.r : 0},${rgb.g !== "" ? rgb.g : 0},${rgb.b !== "" ? rgb.b : 0})`
        rgbAll.hex = rgbToHex(Number(rgb.r),Number(rgb.g),Number(rgb.b))
        
    }
    generatePalette()

    const copyContent = async (text) => {
        
        try {
          await navigator.clipboard.writeText(text);
          console.log('Content copied to clipboard ' + text);
          lastCopiedColor.color = text
        } catch (err) {
          console.error('Failed to copy: ', err);
        }
        showSnackBar.show = true

        setTimeout(() => {
            showSnackBar.show = false
        }, 3000);
    }

    

   /* test case */
   /* var i = 1;               

    function myLoop1() {      
    setTimeout(function() {  
        console.log(i);   
        rgb.r = i
        i++
        generatePalette()                
        if (i < 256) {          
        myLoop1();            
        }                   
    }, 50)
    }

    var j = 1;            

    function myLoop2() {       
    setTimeout(function() {  
        console.log(j);   
        rgb.g = j
        j++
        generatePalette()               
        if (j < 256) {         
        myLoop2();            
        }                     
    }, 100)
    }
    var k = 1;                 
    function myLoop3() {       
    setTimeout(function() {   
        console.log(k);  
        rgb.b = k
        k++
        generatePalette()             
        if (k < 256) {        
        myLoop3();           
        }                       
    }, 150)
    } */

    /* myLoop1();
    myLoop2();
    myLoop3();  */
   

    watch(rgb, (oldRgb, newRgb)=>{
        
        generatePalette()
        /* console.log(rgb) */
    })

    watch(() => rgbAll.rgb, (oldRgbAll, newRgbAll) => {

        let tWithCommas
        let tWithoutCommas
        let tWithBrackets
        let tWithoutBrackets
        if(rgbAll.rgb.includes(",")){
            tWithCommas = rgbAll.rgb
            tWithoutCommas = rgbAll.rgb.replaceAll(",","")
        }
        if(rgbAll.rgb.includes("(") || rgbAll.rgb.includes(")")){
            tWithBrackets = rgbAll.rgb
            tWithoutBrackets = rgbAll.rgb.replaceAll("(","")
            tWithoutBrackets = tWithoutBrackets.replaceAll(")","")
            /* console.log(tWithoutBrackets) */
        }
        if(rgbAll.rgb == ""){
            rgbAll.rgb = `rgb(${rgb.r},${rgb.g},${rgb.b})`
        }
        else{
            /* console.log(tWithBrackets + " " +tWithoutBrackets) */
            if(tWithCommas.length == tWithoutCommas.length+2 && tWithBrackets.length == tWithoutBrackets.length+2){
                /* console.log(rgbAll.rgb) */
                let text = rgbAll.rgb
                let colors = text.split("(")[1].split(")")[0]
                let colorArray = colors.split(",")
                let r = colorArray[0]
                let g = colorArray[1]
                let b = colorArray[2]
                rgb.r = r
                rgb.g = g
                rgb.b = b
                
            }
            else{
                /* console.log(tWithCommas.length +" "+ tWithoutCommas.length+2) */
                rgbAll.rgb = `rgb(${rgb.r},${rgb.g},${rgb.b})`
            }
        }
    })

    watch(() => rgbAll.hex, (oldRgbAll, newRgbAll) => {
        /* console.log(rgbAll.hex) */
        if(rgbAll.hex == ""){
            rgbAll.hex = rgbToHex(Number(rgb.r),Number(rgb.g),Number(rgb.b))
        } 
        else{
            let text = hexToRgb((rgbAll.hex.replace("#","")))
            let colors = text.split("(")[1].split(")")[0]
            let colorArray = colors.split(",")
            let r = colorArray[0]
            let g = colorArray[1]
            let b = colorArray[2]
            rgb.r = r
            rgb.g = g
            rgb.b = b
        }
        /* console.log(rgbAll)
        console.log(rgb) */

    })

    

</script>

<style scoped>

.hoverRGBHandle:hover ~ .hoverRGB{
    font-weight: bold;
    color: rgb(32, 128, 128);
}
.hoverRGBHandle:hover{
    color: rgb(32, 128, 128);
}

.hoverHEXHandle:hover ~ .hoverHEX{
    font-weight: bold;
    color: rgb(32, 128, 128);
}
.hoverHEXHandle:hover{
    color: rgb(32, 128, 128);
}


.hoverHexShow:hover ~ .showingClassHex{
    display: flex;
}
.hoverRgbShow:hover ~ .showingClassRgb{
    display: flex;
}


</style>