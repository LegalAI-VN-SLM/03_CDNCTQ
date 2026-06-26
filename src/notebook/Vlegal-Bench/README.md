Mở link: https://huggingface.co/datasets/CMC-OPENAI/VLegal-Bench.


VLegal-Bench là gì?
Là bộ benchmark để đánh giá LLM trên tiếng Việt pháp lý, không phải dataset huấn luyện lớn mà là bộ test chuẩn.

Có 5 nhóm task:

Nhóm 1: nhận diện & hồi tưởng (entity, topic, concept, điều luật, schema).

Nhóm 2: hiểu & cấu trúc (relation extraction, element recognition, graph, judgement verification, user intent).

Nhóm 3: suy luận & suy diễn (dự đoán điều khoản, phán quyết, multi-hop reasoning, phát hiện xung đột, ước lượng mức phạt).

Nhóm 4: diễn giải & sinh văn bản (tóm tắt văn bản pháp lý, lập luận tư pháp, ý kiến pháp lý).

Nhóm 5: đạo đức, fairness, bias (phát hiện bias, privacy, consistency đạo đức, hợp đồng bất công).

Cấu trúc thư mục (rút gọn): trong mỗi folder X.Y/ có X_Y.jsonl và prompt_X_Y.py.

Ví dụ:
1.1/ chứa 1_1.jsonl (data NER pháp lý) và prompt_1_1.py (cách format prompt để test model).


task_type_mapping = {
    '1_1': 'multiple_choices',
    '1_2': 'multiple_choices',
    '1_3': 'multiple_choices',
    '1_4': 'multiple_choices',
    '1_5': 'multiple_choices',
    '2_1': 'multiple_choices',
    '2_2': 'multiple_choices',
    '2_3': 'generation',
    '2_4': 'multiple_choices',
    '2_5': 'multiple_choices',
    '3_1': 'multiple_choices',
    '3_2': 'multiple_choices',
    '3_3': 'multiple_choices',
    '3_4': 'multiple_choices_imbalance',
    '3_5': 'multiple_choices',
    '4_1': 'generation',
    '4_2': 'generation',
    '4_3': 'generation',
    '5_1': 'multiple_choices',
    '5_2': 'multiple_choices',
    '5_3_legal_ethics_cases': 'multiple_choices',
    '5_3_law_vs_ethics': 'multiple_choices',
    '5_4': 'multiple_choices'
}