package main


import (
	//"encoding/json"
	"context"
	"fmt"
	//"io/ioutil"
	"strconv"
	"os"
	"net/http"
	"time"
	"github.com/PuerkitoBio/goquery"
	"sort"
	//"bytes"

)

// func getJsonDict(data json.RawMessage) (map[string]json.RawMessage, error) {
// 	dict := make(map[string]json.RawMessage)
// 	err := json.Unmarshal(data, &dict)
// 	if err != nil {
// 		return nil, err
// 	}
// 	return dict, nil
// }

func getMap(m map[string]string, f *os.File) {
	type str struct{
		key string
		value string
	}

	var slice []str

	for k, v := range m {
		slice = append(slice, str{k, v})
	}

	sort.Slice(slice, func (i, j int) bool {
		return slice[i].value > slice[j].value
	})

	for i,k := range slice {
		f.WriteString(strconv.Itoa(i+1) + ": "+ k.key + "\t"+ k.value + "\n")	
	}
}

func httpRequest(url string, m map[string]string, timeout int) (string, error) {
	client := &http.Client{Timeout: time.Duration(timeout) * time.Second}
	request, err := http.NewRequest("GET", url, nil)
	if err != nil {
		fmt.Println(err)
		return "", err
	}


	request.Header.Set("User-Agent", "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/89.0.4389.90 Safari/537.36") //

	response, err := client.Do(request.WithContext(context.TODO()))
	if err != nil {

		return "", err
	}
	defer response.Body.Close()

	// reqBytes,_:= ioutil.ReadAll(response.Body)
	// fmt.Print(string(reqBytes))

	name := make(map[int]string)
	rate := make(map[int]string)
	doc, err := goquery.NewDocumentFromReader(response.Body) 
	if err != nil {
		return "", err
	}
	doc.Find(".title:first-child").Each(func(i int, s *goquery.Selection){ 
		content := s.Text()
		name[i] = content
	})
	doc.Find(".rating_num").Each(func(i int, s *goquery.Selection){
		rating := s.Text()
		rate[i] = rating
	})

	for k,v := range name {
		m[v] = rate[k]
	}

	return "",nil
	// if err != nil {
		
	// 	return "", err
	// }
	// return string(body),nil

	// dict, err := getJsonDict(body)
	// if err != nil {
	// 	return nil, err
	// }
	// return dict["data"], nil
}

func main(){
	m := make(map[string]string)
	f,_ := os.Create("douban-movie-top250.txt")
	defer f.Close()

	for i:=0; i<250; i+=25 {
		url := "https://movie.douban.com/top250?start="+ strconv.Itoa(i) +"&filter="
		httpRequest(url, m, 10)
	}

	getMap(m, f)

}
