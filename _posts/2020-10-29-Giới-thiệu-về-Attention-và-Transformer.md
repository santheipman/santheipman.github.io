---
title: "Giới thiệu về Attention và Transformer"
categories:
  - Kiến trúc mạng Neural
tags:
  - attention
  - transformer
---

Sự ra đời của kiến trúc Transformer năm 2017 như một cuộc cách mạng trong việc xây dựng kiến trúc mạng neuron, nó mang đến sự nhảy vọt về kết quả trong nhiều bài toán NLP, hơn nữa Transformer còn là tiền thân của các mô hình mạnh mẽ sau này như BERT, GPT-2, XLNet, ... Ngoài ra, năm 2020 còn chứng kiện sự "lấn sân" của Transformer sang lĩnh vực computer vision qua các bài báo như *"An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale"* bởi Google hay *"End-to-End Object Detection with Transformers"* bởi Facebook.

Tuy mạnh mẽ nhưng thành phần chính của Transformer lại chỉ dựa trên một kĩ thuật đơn giản tên là attention. Trong bài viết này, ta sẽ tìm hiểu về một mô hình Encoder-Decoder Seq2Seq cổ điển hoạt động như thế nào, kĩ thuật attention giúp cải tiến mô hình này ra sao, qua đó hiểu được ý tưởng chung của attention và từ đó có cái nhìn tổng quan về kiến trúc Transformer.

# Mục lục

1. Mô hình Encoder-Decoder Sequence to Sequence (Seq2Seq)
2. Kĩ thuật Attention (Attention mechanism)
3. Transformer

# Mô hình Encoder-Decoder Sequence to Sequence (Seq2Seq)

Năm 2014, Google lần đầu tiên đề xuất [mô hình  Seq2Seq](https://arxiv.org/pdf/1409.3215.pdf). Mô hình này  mã hóa  (encode) một  chuỗi đầu vào thành một vector, rồi sau đó giải mã (decode) vector đó để cho ra chuỗi đầu ra.  

![example_machine_translation](/assets/images/1_example_machine_translation.png)

Một  ứng dụng điển hình của mô hình này là dịch máy (machine translation). Ví dụ khi ta sử dụng Google Translate để dịch câu "I go to school" sang tiếng Việt thì chuỗi đầu vào trong trường  hợp này là câu tiếng Anh "I go to school", còn chuỗi đầu ra là câu tiếng Việt "tôi đi học".

> Our method uses a multilayered Long Short-Term Memory (LSTM) to map the input sequence to a vector of a fixed dimensionality, and then another deep LSTM to decode the target sequence from the vector.
> -- Sequence to Sequence Learning with Neural Networks, 2014.

![seq2seq](/assets/images/1_seq2seq.png)

**Encoder** là mạng RNN, có nhiệm vụ mã hóa chuỗi đầu vào  thành một **vector có số chiều cố định (fixed-dimensional vector)**.

**Decoder** là một mạng RNN khác, nó giải mã vector trên để thu được chuỗi đầu ra. Cụ thể, với timestep đầu tiên của decoder thì hidden state truyền vào chính là vector mã hóa, còn từ được truyền vào là kí tự bắt đầu câu (start of sentence - SOS). Từ vựng đầu ra tại timestep này chính là từ vựng đầu vào tại timestep sau. Theo ví dụ trên, timestep thứ nhất cho ra từ "tôi", từ này được truyền vào làm từ vựng đầu vào tại timestep thứ hai, tiếp tục ta thu được từ "đi", ta lại lấy từ này truyền vào timestep thứ ba,... cứ làm như vậy cho đến khi thu được kí tự kết thúc câu (end of sentence - EOS) thì dừng.

Điểm mạnh của mô hình Seq2Seq chính là **độ dài chuỗi đầu vào và chuỗi đầu ra không cần phải bằng nhau** [[1]][understand_seq2seq]. Encoder cứ truyền hết các từ có trong chuỗi đầu vào và mã hóa chúng thành một vector, rồi decoder lấy  vector đó và cứ giải mã ra các từ cho đến khi nào gặp kí tự kết thúc thì thôi. Trong thực tế khi dịch giữa hai ngôn ngữ thì thường độ dài của câu trong ngôn ngữ này sẽ khác độ dài của câu trong ngôn ngữ kia.

Mô hình Seq2Seq hoạt động rất hiệu quả đối với chuỗi đầu vào không quá dài, tuy nhiên khi chuỗi đầu vào càng dài thì hiệu quả của mô hình càng giảm [[2]][coursera_nlp_c4]. Nguyên nhân của hiện tượng này nằm ở việc số chiều của vector mã hóa là cố định. Giả sử nếu vector mã hóa có số chiều là 500 thì dù chuỗi đầu vào có độ dài là 40 hay 4000 thì encoder đều phải mã hóa nó thành một vector 500 chiều, lượng thông tin lớn phải mã hóa lại thành một vector nhỏ có số chiều cố định, điều này gây ra hiện tượng thắt cổ chai (bottleneck).

Để hiểu hơn về Seq2Seq ta có thể xem một [tutorial](https://blog.keras.io/a-ten-minute-introduction-to-sequence-to-sequence-learning-in-keras.html) đơn giản và dễ hiểu được viết bởi [Francois Chollet](https://twitter.com/fchollet) hướng dẫn cài đặt Seq2Seq với Keras.

# Kĩ thuật Attention (Attention mechanism)

Để khắc phục nhược điểm của mô hình Seq2Seq cổ điển, ta có thể áp dụng một kĩ thuật đơn giản mà hiệu quả mang tên attention.

Ý tưởng chính của kĩ thuật attention thực ra rất tự nhiên, thuận theo lối hành động của con người.

Trước hết ta hãy thử làm một việc sau: Dịch tiếp câu dưới đây từ tiếng Anh sang tiếng Việt. Trong quá trình dịch, hãy để ý xem mắt mình đưa đi đâu và từng từ mình dịch ra dựa trên những từ nào.

*Tiếng Anh*:

Molave &emsp;&emsp; was &emsp;&emsp; the &emsp;&emsp; fourth &emsp;&emsp; storm &emsp;&emsp; to &emsp;&emsp; hit &emsp;&emsp; our &emsp;&emsp; country &emsp;&emsp; this &emsp;&emsp; month.

*Dịch sang tiếng Việt*:

Molave ​​&emsp;&emsp; là &emsp;&emsp; cơn_bão &emsp;&emsp; ________________________.

.

.

Đáp án tham khảo:

Molave ​​&emsp; là &emsp; cơn_bão &emsp; *thứ_tư &emsp; đổ_bộ &emsp; vào &emsp; nước &emsp; ta &emsp; trong &emsp; tháng &emsp; này*.

Rất có thể, ban đầu ta đọc cả câu tiếng anh, rồi nhìn vào câu tiếng Việt thấy câu đã được dịch đến từ "cơn_bão", tiếp đó ta chú ý đến từ "fourth", từ "storm" để có thể dịch và viết từ tiếp theo là từ "thứ_tư" vào câu dịch.

Và đó cũng chính là ý tưởng chủ đạo của kĩ thuật Attention: tại decoder ta tạo thêm các [lối](https://www.youtube.com/watch?v=KKc_RMln5UY) đi nối đến tất cả các từ trong chuỗi đầu vào nhằm giúp cho decoder có thể truy cập thông tin từ các từ ấy một cách trực tiếp hơn.

![seq2seq_with_attention](/assets/images/1_seq2seq_with_attention.png)
Hình trên minh họa kĩ thuật attention tại một timestep của decoder (các timestep khác đã được vẽ đơn giản hóa). Thông tin mà Attention nhận vào gồm hai phần: một là thông tin của tất cả các từ trong chuỗi đầu vào (hidden state của tất cả các timestep trong encoder) và hai là thông tin từ timestep trước đó trong decoder (hidden state của timestep ngay trước đó trong decoder).

Attention sẽ gán các trọng số khác nhau cho thông tin của các từ trong chuỗi đầu vào, trọng số này thể hiện tầm quan trọng của mỗi từ trong số đó bởi vì tại mỗi timestep thì decoder chỉ cần chú ý đến một số từ nhất định trong chuỗi đầu vào. Ví dụ để dịch ra được từ "thứ_tư" thì decoder cần chú ý nhiều đến từ "fourth" và từ "storm" hơn là chú ý đến các từ "Molave", "was", "the". Chi tiết cách hoạt động bên trong của attention sẽ được đề cập ở bài viết sau.

Ta có thể hiểu rằng, dịch theo lối của mô hình Seq2Seq cổ điển giống như việc một người đọc hết một câu tiếng Anh rồi cất câu đó sang một bên, sau đó dịch một hơi hết toàn bộ câu đó ra tiếng Việt mà không nhìn lại câu tiếng Anh đó. Trái lại, nếu dùng kĩ thuật attention thì trong quá trình dịch, người đó sẽ đánh mắt nhìn lại một số từ trong câu tiếng Anh có liên quan đến đoạn nguời đó đang dịch đến.

![att_mr-bean-cheating-on-exam.jpg)](/assets/images/1_att_mr-bean-cheating-on-exam.jpg)

Không chỉ được ứng dụng trong NLP, kĩ thuật attention còn được mang vào lĩnh vực computer vision, nhận được quan tâm từ giới nghiên cứu và từ đó cả một họ các kĩ thuật attention ra đời. Dấu ấn lớn nhất là vào năm 2017, bài báo "Attention Is All You" của Google công bố một kiến trúc mạng neuron mới hoàn toàn dựa trên các kĩ thuật attention, kiến trúc này mang tên Transformer.

# Transformer

Kiến trúc Transformer đơn giản nhưng mạnh mẽ về cả độ chính xác và tốc độ. Nó dựa hoàn toàn vào các kĩ thuật attention và không cần đến các tầng CNN và RNN. Các tầng Add & Norm và Positional Encoding như ta thấy ở hình vẽ bên dưới (từ bài báo "Attention Is All You Need") được thêm vào nhằm góp phần tăng hiệu quả của mô hình. 

![](/assets/images/1_transformer_.png){: .align-center}

> We propose a new simple network architecture, the Transformer,
based solely on attention mechanisms, dispensing with recurrence and convolutions
entirely. Experiments on two machine translation tasks show these models to
be superior in quality while being more parallelizable and requiring significantly
less time to train.
> -- Attention Is All You Need, 2017.

Sự ra đời của kiến trúc Transformer như một cuộc cách mạng trong việc xây dựng kiến trúc mạng neuron, nó mang đến sự nhảy vọt về kết quả trong nhiều bài toán NLP, hơn nữa Transformer còn là tiền thân của các mô hình mạnh mẽ sau này như BERT, GPT-2, XLNet, ... Ngoài ra, năm 2020 còn chứng kiện sự "lấn sân" của Transformer sang lĩnh vực computer vision qua các bài báo như *"An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale"* bởi Google hay *"End-to-End Object Detection with Transformers"* bởi Facebook.

Ở bài viết sau, ta sẽ bàn về chi tiết các kĩ thuật attention được sử dụng trong Transformer, bên cạnh đó là các kĩ thuật khác được sử dụng trong Transformer nhằm tăng hiệu quả của mô hình như residual connection, positional encoding, normalization,...

# Đọc thêm (sắp hoàn thành)

https://lilianweng.github.io/lil-log/2018/06/24/attention-attention.html

https://atcold.github.io/pytorch-Deep-Learning/en/week12/12-1/

https://www.coursera.org/lecture/nlp-sequence-models/attention-model-intuition-RDXpX

https://www.youtube.com/watch?v=53YvP6gdD7U&t=120s

https://jalammar.github.io/visualizing-neural-machine-translation-mechanics-of-seq2seq-models-with-attention/

# Tài liệu tham khảo (sắp hoàn thành)


[public repository][dill]

[coursera_nlp_c4]:<https://www.coursera.org/programs/275e1cae-d93c-4fd5-ac5c-0dd708eb0945/browse?currentTab=MY_COURSES&productId=_U5cASTxEemuhAoKFebZeA&productType=s12n&query=Natural+Language+Processing&showMiniModal=true>

[understand_seq2seq]:<https://towardsdatascience.com/understanding-encoder-decoder-sequence-to-sequence-model-679e04af4346>
[dill]: <https://github.com/joemccann/dillinger>
