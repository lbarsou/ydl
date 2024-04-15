#! /bin/bash

if [ -z $1 ]
then url=$(xclip -o -selection clipboard)
else url=$1
fi

icon="youtube-dl"
ask=$(yad --image=$icon --image-on-top --center --height=305 --list --text="Youtube-dl" --checklist --column="" --column="Action" FALSE "Best Video" FALSE "MP4" FALSE "720p" FALSE "480p" FALSE "Best Audio" FALSE "M4A" FALSE "Format selection" FALSE "Cut")  
name1=$(yt-dlp --cookies-from-browser chrome --get-filename -o '%(title)s' $url)
if [[ ${#name1} > 150 ]]
then name1=$(echo $name1 | cut -c -150)
fi


ydl ()		{
		height=$(yt-dlp --cookies-from-browser chrome $format --get-filename -o '%(height)s' $url)
		if [ $height = NA ]
		then name2=$(yt-dlp --cookies-from-browser chrome $format --get-filename -o '- %(uploader)s.%(ext)s' $url)
		else name2=$(yt-dlp --cookies-from-browser chrome $format --get-filename -o '- %(uploader)s (%(height)sp).%(ext)s' $url)
		fi
		name="$name1 $name2"

		notify-send --urgency low "Téléchargement" "$name" -i "$icon"
		if yt-dlp --cookies-from-browser chrome $format -o "~/Downloads/Ydl/$name" "$url"
		then notify-send --urgency normal   "Téléchargement terminé" "$name" -i "$icon"
		else notify-send --urgency critical "Échec du téléchargement" "$name" -i "$icon"
		fi
		}

ydlcut () 	{
		height=$(yt-dlp --cookies-from-browser chrome $format --get-filename -o '%(height)s' $url)
		if [ $height = NA ]
		then name2=$(yt-dlp --cookies-from-browser chrome $format --get-filename -o '- %(uploader)s ('$T').%(ext)s' $url)
		else name2=$(yt-dlp --cookies-from-browser chrome $format --get-filename -o '- %(uploader)s ('$T' %(height)sp).%(ext)s' $url)
		fi
		name="$name1 $name2"
		
		notify-send --urgency low "Téléchargement" "$name" -i "$icon"
		if yt-dlp --cookies-from-browser chrome --external-downloader ffmpeg --external-downloader-args "-ss $start -to $end" $format -o "~/Downloads/Ydl/$name" "$url"
		then notify-send --urgency normal   "Téléchargement terminé" "$name" -i "$icon"
		else notify-send --urgency critical "Échec du téléchargement" "$name" -i "$icon" 
		fi
	 	}	

if [[ "$ask" == *"Cut"* ]]
then 	tcodes=$(yad --image=$icon --center --form --title='Cut' --text='Saisie timecodes HHMMSS' --field=Début --field=Fin "000" "000" | sed 's/|//gi')
	if [[ ${#tcodes} != 12 ]] || [[ $tcodes != ?(-)+([0-9]) ]] 
	then notify-send --urgency normal   "Téléchargement annulé" "Timecodes erronés ou manquants" -i "$icon" && exit 1				
	else 	start=$(sed -e 's/../:&/2g' <<< "${tcodes:0:6}") ; end=$(sed -e 's/../:&/2g' <<< "${tcodes:6:6}")  
		start1=$(date -u -d "$start" +"%s") ; end1=$(date -u -d "$end" +"%s") ; duration=$(date -u -d "0 $end1 sec - $start1 sec" +%T) 	
		H=$(date -u -d "$duration" +%-H); M=$(date -u -d "$duration" +%-M); S=$(date -u -d "$duration" +%-S); T=;  			
		(( $H > 0 )) && T="$H"h ; (( $M > 0 )) && T="$T$M"m ; (( $S > 0 )) && T="$T$S"s 
		downloader='ydlcut'
	fi
else downloader='ydl'  
fi

case "$ask" in
 	*"Format selection"*)
 	yt-dlp --cookies-from-browser chrome -F $url | sed -e '1,/-/d' | sed 's/^/false\n/' > /tmp/ydlinfo.list   && 
	selc=$(yad --center --width=800 --height=360 --list --text="Sélection vidéo et/ou audio" --checklist --column="" --column="" < /tmp/ydlinfo.list | cut -d '|' -f2 | cut -d ' ' -f1 | sed -e ':s;N;s/\n/+/;bs')
	if 	[ -z $selc ]
	then 	notify-send --urgency normal "Téléchargement annulé" "Aucun format spécifié" -i "$icon" && exit 1
	else 	format="-f $selc" && $downloader
	fi ;;
esac && case "$ask" in
 	*"Best Video"*) format="-f best" && $downloader ;;
esac && case "$ask" in
 	*"MP4"*) 	format="-f bestvideo[ext=mp4]+bestaudio[ext=m4a]/best[ext=mp4]/best" && $downloader ;;
esac && case "$ask" in
 	*"720p"*) 	format="-f bestvideo[height<=720][ext=mp4]+bestaudio[ext=m4a]/best[height<=720][ext=mp4]/best" && $downloader ;;
esac && case "$ask" in
 	*"480p"*) 	format="-f bestvideo[height<=480][ext=mp4]+bestaudio[ext=m4a]/best[height<=480][ext=mp4]/best" && $downloader ;;
esac && case "$ask" in
 	*"Best Audio"*) format="-f bestaudio" && $downloader ;;
esac && case "$ask" in
 	*"M4A"*) 	format="-f bestaudio[ext=m4a]" && $downloader ;;
esac
